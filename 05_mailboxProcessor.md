### シンプルなエージェント
```fsharp
/// エージェントを作成する（　Queueに入れるタイプを指定する、async式を返す式を指定する）
let simpleAgent = new MailboxProcessor<string>(fun _ -> async { () })

/// Queueに文字を入れる
simpleAgent.Post("hello")

/// Queueに入ってる文字列の数を調べる
simpleAgent.CurrentQueueLength |> stdout.WriteLine // 1

simpleAgent.Post("world")
simpleAgent.CurrentQueueLength |> stdout.WriteLine // 2

/// Queueに入ってる文字列をすべて調べる
simpleAgent.TryScan( fun x ->
    stdout.WriteLine(x)
    if x = null then Some(async{()}) else None
,timeout = 1) |> Async.RunSynchronously |> ignore
// hello
// world

/// Queueからエージェント側に文字列を取得する
simpleAgent.Receive() |> Async.RunSynchronously |> stdout.WriteLine // hello

/// Queueに入ってる文字列の数を調べる
simpleAgent.CurrentQueueLength |> stdout.WriteLine // 1

simpleAgent.Receive() |> Async.RunSynchronously |> stdout.WriteLine // world
simpleAgent.CurrentQueueLength |> stdout.WriteLine // 0
```

### エージェントから値を取得する
```fsharp
let replyAgent = MailboxProcessor.Start( fun inbox ->
    async {
        let! (ch:AsyncReplyChannel<_>) = inbox.Receive()
        ch.Reply "hello"
    }
)
replyAgent.PostAndReply id |> stdout.WriteLine
/// hello
```

### FizzBuzzしてみた
```fsharp
let (|Div|_|) by n = if n % by = 0 then Some Div else None

let fizzbuzz = function
    | Div 15 -> "FizzBuzz"
    | Div  5 -> "Buzz"
    | Div  3 -> "Fizz"
    | n      -> string n

type Command = Add of int | Fetch of AsyncReplyChannel<string list>

let fizzbuzzAgent = MailboxProcessor.Start( fun inbox ->
    
    let rec loop lst = async{
        let! msg = inbox.Receive()
        match msg with
        | Add value ->
            return! loop ( fizzbuzz value :: lst )
        | Fetch ch ->
            ch.Reply(lst)
            return! loop lst
    }
    loop []
)

[1..100] |> List.iter( fun n -> fizzbuzzAgent.Post(Add n) )

fizzbuzzAgent.PostAndReply(Fetch)
|> List.rev
|> List.iter stdout.WriteLine
```
結果
```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
16
17
以下略
```

### ちょっとした電卓
```fsharp
type Command = AllClear | Add of int | Enter of AsyncReplyChannel<int * Command list>
    
let CalcAgent = MailboxProcessor.Start( fun inbox -> 

    let rec loop n lst = async {
        match inbox.Receive() |> Async.RunSynchronously with
        | AllClear   -> return! loop 0 []
        | Enter repl -> repl.Reply( n , lst |> List.rev ) ; return! loop n lst
        | Add value  -> return! loop (value + n) ( Add value   :: lst )
    }
    loop 0 []
)

CalcAgent.Post(Add 8)
CalcAgent.Post(Add 5)
CalcAgent.Post(Add -3)
CalcAgent.PostAndReply(Enter) |> printfn "%A"
/// (10, [Add 8; Add 5; Add -3])

CalcAgent.Post(AllClear)

CalcAgent.Post(Add 1)
CalcAgent.Post(Add 5)
CalcAgent.Post(Add -2)
CalcAgent.PostAndReply(Enter) |> printfn "%A"
/// (4, [Add 1; Add 5; Add -2])
```
