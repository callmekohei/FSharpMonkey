FizzBuzz by FSharp
---
```
１：手続き型 (Imperative)
２：関数型 (functional)
３：オブジェクト指向 (Object-orientation)
４：コンピュテーション式 (computation expression)
５：モナド (monad)
６：アクターモデル (actor model)
７：async ( parallel )
```

####コード１（手続き型）Imperative
```fsharp
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
####コード２（関数型）functional
```fsharp
module fizzbuzz02 =

    let (|Div|_|) by n = if n % by = 0 then Some Div else None

    let fizzbuzz = function
        | Div 15 -> "FizzBuzz"
        | Div  5 -> "Buzz"
        | Div  3 -> "Fizz"
        | n      -> string n

    [1..100] |> List.map(fizzbuzz) |> printfn "%A"
```

####コード３（オブジェクト指向）Object-orientation
```fsharp
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

####コード４（コンピュテーション式をつかう）computation expression
```fsharp
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

####コード５（モナドを使う）monad
```fsharp
// author: @zecl
// http://zecl.hatenablog.com/entry/20110711/p1
module fizzbuzz_monad =
    
    type M<'T> = M of 'T 

    let inline (>>=) (M x) f : M<'U> = f x
    let mreturn x : M<'T> = M x

    type MonadBuilder () =
        member this.Return (x) = mreturn x
        member this.Bind (m,f) = m >>= f

    let m = MonadBuilder ()

    let fizz = function
        | x when x % 3 = 0 -> m { return x,"Fizz" }
        | x -> m { return x,"" }

    let buzz = function
        | x,s when x % 5 = 0 -> m { return x, s + "Buzz" }
        | x,s -> m { return x,s }

    let printFB  = function 
        | M(x,"") -> printfn "%A" x
        | M(_,y)  -> printfn "%s" y

    let fizzbuzz = Seq.iter (fun x -> (fizz x >>= buzz) |> printFB)

    [1..100] |> fizzbuzz
    System.Console.ReadLine () |> ignore

```

####コード６（アクターモデル）actor model
```fsharp
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

#### code7 async ( parallel )
```fhsarp
module FizzBuzz_Parallel =

    let fizzbuzz (n:int)  = async {

        let (|Div|_|) by n = if n % by = 0 then Some Div else None

        let fb = function
            | Div 15 -> "FizzBuzz"
            | Div  5 -> "Buzz"
            | Div  3 -> "Fizz"
            | n      -> string n

        return System.Console.WriteLine("n: {0} ---> {1}", n , (fb n))

        }

    [1..15]
    |> Seq.map fizzbuzz 
    |> Async.Parallel 
    |> Async.RunSynchronously
    |> ignore
```
