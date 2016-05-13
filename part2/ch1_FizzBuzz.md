FizzBuzz by FSharp
---
```
１：手続き型 (Imperative)
２：関数型 (functional)
３：オブジェクト指向 (Object-orientation)
４：コンピュテーション式 (computation expression)
５：モナド (monad)
６：アクターモデル (actor model)
```

コード１（手続き型）Imperative
---
```
module fizzbuzz01 =

    let fizzbuzz n =
        if    n % 15 = 0 then 
            "FizzBuzz"
        elif  n %  5 = 0 then
            "Buzz"
        elif  n %  3 = 0 then
            "Fizz"
        else
            string n

    for v in 1..100 do
        printfn "%A" ( fizzbuzz v )
```
コード２（関数型）functional
---
```
module fizzbuzz02 =

    let (|Div|_|) by n = if n % by = 0 then Some Div else None

    let fizzbuzz = function
        | Div 15 -> "FizzBuzz"
        | Div  5 -> "Buzz"
        | Div  3 -> "Fizz"
        | n      -> string n

    [1..100] |> List.map(fizzbuzz) |> printfn "%A"
```

コード３（オブジェクト指向）Object-orientation
---
```
// author: @yukitos
// https://gist.github.com/yukitos/06e4d825ccc33047ec8d
module fizzbuzz_yukitos =

    type IFizzBuzz =
        abstract member check : int -> bool
        abstract member getVal : unit -> string
            
    type Fizz() =
        interface IFizzBuzz with
            member x.check n = (n % 3 = 0)
            member x.getVal() = "Fizz"
                
    type Buzz() =
        interface IFizzBuzz with
            member x.check n = (n % 5 = 0)
            member x.getVal() = "Buzz"

    type FizzBuzz(handlers : IFizzBuzz list) =
        member x.eval n =
            let result = 
                ("", handlers)
                ||> Seq.fold (fun acc h ->
                    if h.check(n) then acc + h.getVal()
                    else acc)
            if result = "" then string n
            else result

    let fb =
        new FizzBuzz(
            [
                new Fizz()
                new Buzz()
            ])
    [1..100] |> List.map(fb.eval) |> printfn "%A"
```

コード４（コンピュテーション式をつかう）computation expression
---
```
module FizzBuzz1 =

    type fizzbuzzBuilder () =

        let (|Div|_|) by n = if n % by = 0 then Some Div else None

        member this.Bind (x, f) = List.map f x 
        
        member this.Return x = 
            match x with
            | Div 15 -> "FizzBuzz"
            | Div  5 -> "Buzz"
            | Div  3 -> "Fizz"
            | _ ->  string x

    let fizzbuzz = new fizzbuzzBuilder()

    fizzbuzz { let! x = [1..100] in return (x) } |> printfn "%A"
```

コード５（モナドを使う）monad
---
変更予定
```
// author: @zecl x @callmekohei x @gab_km
// http://zecl.hatenablog.com/entry/20110711/p1
module FizzBuzz2 =

    type M<'T> = M of 'T 

    type MonadBuilder () =
        member this.Return x = M x

    let m = new MonadBuilder()

    let fizz = function
    | x when x % 3 = 0 -> m { return x,"Fizz" }
    | x -> m { return x,"" }

    let buzz = function
    | M (x,s) when x % 5 = 0 -> m { return x, s + "Buzz" }
    | M (x,s) -> m { return x,s }

    [1..100] |> List.map (fizz) |> List.map (buzz) |> printfn "%A"
```

コード６（アクターモデル）actor model
---
```
module FizzBuzz_used_agent =
    
    type msg = Cont of string | Stop | Fetch of AsyncReplyChannel<string>

    let agent =
        new MailboxProcessor<msg>( fun inbox ->
            
            let mutable n = 0

            let rec loop n = async {

                let! ms = inbox.Receive ()
                match ms with
                | Cont m ->
                    return! loop m
                | Stop ->
                    printfn "stop"
                    return ()
                | Fetch replyChannel ->
                    replyChannel.Reply n
                    return! loop (string ( System.Int32.Parse(n) + 1))
            }
            loop ""
        )

    agent.Start ()

module main =
    
    open FizzBuzz_used_agent
    
    let (|Div|_|) by n = if n % by = 0 then Some Div else None

    [100..-1..1] 
    |> List.map (fun n -> string n)
    |> List.map (fun n -> Cont n)
    |> List.map agent.Post
    
    for v in 1.. agent.CurrentQueueLength do
        match  (System.Int32.Parse( agent.PostAndReply ( fun reply -> Fetch reply )))  with
        | Div 15 -> printfn "FizzBuzz"
        | Div  5 -> printfn "Buzz"
        | Div  3 -> printfn "Fizz"
        | _ ->  printfn "%A" (string v)
    
    agent.Post Stop
```
