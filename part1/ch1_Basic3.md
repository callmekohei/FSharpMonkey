
Chapter1  Basic 3
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

