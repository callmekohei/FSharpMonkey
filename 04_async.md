`Aync<'T>`に関してのちょっとしたメモ

### 0. Prepare
`async{...}` とすると　`Async<'T>`になる

.  

`let!`とか`do!`とかのbang`!`は`Asyncをはぎとる。つまり`Async<'T>`は`'T`に、`Async<Async<'T>>`は`Async<'T>`になる。

### 1. RunSynchronously and Start
`async block`をバックグラウンドで実行させるかどうかのメソッド
.  

バックグラウンドで実行させない
```fsharp
// always hello world

async{
    stdout.WriteLine("hello")
}|> Async.RunSynchronously

stdout.WriteLine("world")
```
バックグラウンドで実行させる
```fsharp
// hello world or world hello or world

async{
    stdout.WriteLine("hello")
}|> Async.Start

stdout.WriteLine("world")
```

### 2. Parallel

実行順序は保証されない
```fsharp
// hello world or world hello
let hello1 = async { stdout.WriteLine("hello") }
let world1 = async { stdout.WriteLine("world") }

[hello1;world1]
|> Async.Parallel
|> Async.RunSynchronously
```

実行順序は保証されないがリストの場所は保証される
```fsharp
// ["hello";"world"]
let hello2 = async { return "hello" }
let world2 = async { return "world" }

[hello2;world2]
|> Async.Parallel
|> Async.RunSynchronously
|> printfn "%A"
```

### 3. Order in async block
`asy nc block`の中は上から下に実行される
```fsharp
// hello world

let hello = async { do stdout.WriteLine("hello") }
let world = async { do stdout.WriteLine("world") }

async {
    do! hello
    do! world
} |> Async.RunSynchronously
```

### 4. Wait async (StartChild)
 
`StartChild`を使うことで`async block`の中で`Async<'T>`をバックグラウンド処理できるようになる

```fsharp
let wakeUp = async {
    do! Async.Sleep(1500)
    do stdout.WriteLine("Wake up!")
}

let goodMorning = async { stdout.WriteLine("goodMorning") }
let iamSleep    = async { stdout.WriteLine("zzz...") }

async { 
    let! (iamWakeUp:Async<unit>) = Async.StartChild( wakeUp, 100 )
    try
        do! iamWakeUp
    with :? exn ->
        do! iamSleep 
} |> Async.RunSynchronously
```
```fsharp
// world hello
let hello = async {
    do! Async.Sleep(100)
    do stdout.WriteLine("hello") }

let world = async { do stdout.WriteLine("world") }

async {
    let! child = hello |> Async.StartChild
    do! world
    do! child
} |> Async.RunSynchronously
```
.  

`Task`を使った例
.  


`AwaitIAsyncResult`を使うことで待機させることができる

.  

無期限に待つ例
```fsharp
let apple  = async { return 100 }
let banana = async { return 70  }
let cherry = async { return 350 }
let sum = [ apple; banana; cherry ]

let eating = async { do stdout.WriteLine("Taste good!") }

async {
    let! (withinMoney:bool) = Async.AwaitIAsyncResult( sum |> Async.Parallel |> Async.StartAsTask)
    do!  eating
} |> Async.RunSynchronously
```


タイムアウトを指定した例
```fsharp
let wakeUp = async {
    do! Async.Sleep(1500)
    return stdout.WriteLineAsync("Wake up!")
}

let goodMorning = async { stdout.WriteLine("goodMorning") }
let iamSleep = async { stdout.WriteLine("zzz...") }

async {
    let! (wakeUpWithinTime:bool) = Async.AwaitIAsyncResult( wakeUp |> Async.StartAsTask ,100 )
    if    wakeUpWithinTime
    then  do! goodMorning
    else  do! iamSleep
} |> Async.RunSynchronously
```

### 5. Exception and Cancel
```fsharp
let green  = fun _ ->  stdout.WriteLine("green") 
let yellow = fun _ ->  stdout.WriteLine("yellow") 
let red    = fun _ ->  stdout.WriteLine("red") 

// 通常の動作
let signalGreen = async { do () }
Async.StartWithContinuations ( signalGreen, green, yellow, red )

// エラー処理
let signalYellow:Async<obj> = async {
    return raise <| new System.Exception()
}
Async.StartWithContinuations ( signalYellow, green, yellow, red )

// キャンセル処理
let signalRed (tokenSource:System.Threading.CancellationTokenSource)  = async {
    do! Async.Sleep(1000)
}

let tokenSource = new System.Threading.CancellationTokenSource()
Async.StartWithContinuations ( signalRed tokenSource , green, yellow, red , tokenSource.Token )
tokenSource.Cancel()
```
