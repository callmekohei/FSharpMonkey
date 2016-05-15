####2-ways expression of function
```fsharp
let twice x = x * 2
5 |> twice              |> printfn "%A" // 10
5 |> ( fun x -> x * 2 ) |> printfn "%A" // 10
```

####Return value
```fsharp
let bookShelf x = x 
let Trash x = x |> ignore

"paper" |> bookShelf |> printfn "%A" // "paper"
"paper" |> Trash     |> printfn "%A" // <null>
```

####Byval and ByRef
```fsharp
// byval
let x = 5      // x is label
let twice a = a * 2

x |> twice |> printfn "%A"


// byref
let y = ref 5  // y is container
let twice_ref ( b:int ref ) =
    b := !b * 2

y  |> twice_ref
!y |> printfn "%A"

```

####Recursive functions
```fsharp
let rec say word i =
    match i with
    | 0 -> ""
    | _ -> printfn "%A" word
           say word (i-1)

("callmekohei",3) ||> say
// "callmekohei"
// "callmekohei"
// "callmekohei"
```

####Higher-order functions on collections
```fsharp
[1;2;3] |> List.map ( fun n -> n * 2 ) |> printfn "%A"
// [2; 4; 6]
```

####Active pattern
```fsharp
// http://luketopia.net/2014/09/11/interesting-active-patterns/
let (|NonEmptyString|) value =
    if System.String.IsNullOrEmpty value
    then failwith "String must be non-empty."
    else value

let sayHello (NonEmptyString name) =
    printfn "Hello, %s." name

sayHello "callmekohei" // Hello, callmekohei.
```
