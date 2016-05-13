```fsharp
module sample00 =

    // 通常の式は return はひつようない
    // legular exp :     let x = 99 in x 

    // computation exp. は return が必要
    // computation exp : let x = 99 in return x 

    let x = 99 in x |> printfn "%A" 
    // 99
```

####Return
```fsharp
module sample01 = 
    
    // return される式（この場合 n ）は Return のパラメーターになる
    
    type myBuilder () =
        member this.Return x = x + 100

    let myBld = new myBuilder()
    
    myBld{ let n = 99 in return n } |> printfn "%A" 
    // 199

module sample02 = 

    type myBuilder () =
        member this.Return x = fun () -> x // 関数でつつみこむ（closure)

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n } |> printfn "%A"
    // <fun:Return@28>
    
    () |> myBld { let n = 99 in return n } |> printfn "%A"
    // 99
```

####Return!
```fsharp
module sample08 = 

   // computation exp.のなかに return! をいれると ReturnFrom に
   // computation exp を渡せます
    
    type myBuilder () =
        member this.ReturnFrom x = x + 500
        member this.Return x     = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n }  |> printfn "%A" 
    // 299

    myBld { let n = 99 in return! n } |> printfn "%A"
    // 599
```



####Bind
```fsharp
module sample05 = 

    // computation exp.のなかに let! をいれると Bind にタプルとしてわたされる
    // tuple = ( 99, (fun x -> x + 200))
    
    type myBuilder () =
        member this.Bind (x, _) = x + 100
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n }  |> printfn "%A" 
    // 299

    myBld { let! n = 99 in return n } |> printfn "%A"
    // 199

module sample06 = 

    // Bind の tuple の第２パラメーターを利用すると式を継続できます
    // この場合式は Return を示してます

    type myBuilder () =
        member this.Bind (x, f) = f (x + 100)
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n }  |> printfn "%A" 
    // 299

    myBld { let! n = 99 in return n } |> printfn "%A"
    // 399
```

####Zero
```fsharp
module sample07 = 

    // Zero は if の else 側がない場合に実行される

    type myBuilder () =
        member this.Zero () = 500
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let x = 99
            if x = 0 then return 0} |> printfn "%A" 
    
    // 500
```



####Delay
```fsharp
module sample04 = 

    // Delay は 最初に実行されます（初期設定みたいな感じ）
    // Delayにわたされるときはクロージャになります

    type myBuilder () =
        member this.Delay  wrapedFunc =  wrapedFunc () + 100
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n } |> printfn "%A" 
    // 399
```


####Run
```fsharp
module sample03 = 

    // Run をメソッドに加えると computaion exp. が実行される

    type myBuilder () =
        member this.Return x = fun () -> x
        member this.Run f = f () + 100

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n } |> printfn "%A"
    // 199
```


####Combine
```fsharp
module sample09 = 

    // Combine は computation exp.を束ねます（combine) 

    type myBuilder () =
        member this.Combine (a,b) =
            printfn "combine"        
            a@b
        member this.Return x =
            printfn "return"
            x
        member this.Delay f = f ()

    let myBld = new myBuilder()

    myBld { let x = [1..3] in return x
            let y = [5..8] in return y} |> printfn "%A"

    //  return
    //  return
    //  combine
    //  [1; 2; 3; 5; 6; 7; 8]

module sample10 = 

    type myBuilder () =
        member this.Combine (a,b) =
            printfn "combine %A %A" a b      
            a@b
        member this.Return x =
            printfn "return"
            x
        member this.Delay(f) = f()

    let myBld = new myBuilder()

    myBld { let x = [1;2] in return x
            let y = [3;4] in return y
            let z = [5;6] in return z } |> printfn "%A" 
    
    //return
    //return
    //return
    //combine [3; 4] [5; 6]
    //combine [1; 2] [3; 4; 5; 6]
    //[1; 2; 3; 4; 5; 6]
```

