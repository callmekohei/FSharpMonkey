
Chapter1  Basic
---
####Pipe
```fsharp
printfn "%s" "ABC"

    is

"ABC" |> printfn "%s"
```

####Printfn  
```fsharp
"ABC" |> printfn "%A" // "ABC"  A is All.

true  |> printfn "%b" // true   b is bool.
'A'   |> printfn "%c" // A      c is char.
"ABC" |> printfn "%s" // ABC    s is string.
9999  |> printfn "%d" // 9999   d is digit.
1.23  |> printfn "%f" // 1.23   f is float.
```

####Comments
```fhsarp
(* block 
   comment *)

// line comment
```
.  
.  
####Bind
`=` shows bind
```fsharp
let letters = "Hello, World!"

letters |> printfn "%A"
// Hello, World!
```

####Asign
`<-` shows asign
```fsharp
let mutable letters = "hello"

letters |> printfn "%A" // "hello"

letters <- "World!"

letters |> printfn "%A" // "World!"
```

####Reference
`x` is container.  
```fsharp
let x = ref "hello"

!x |> printfn "%A" // "hello"

x:= "world"

!x |> printfn "%A" // "world"
```

