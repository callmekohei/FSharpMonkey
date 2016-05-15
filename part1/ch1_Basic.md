
Basic 3
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
Constant, variable and container
---
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
.  
.  
Loop 2
---
####for
```fsharp
for c in "ABC" do
    c |> printfn "%A"

// 'A' 'B' 'C'
```
####while
```fsharp
let mutable i = 0
let str = "ABC"
while str.Length > i do
    str.[i] |> printfn "%A"
    i <- i + 1

// 'A' 'B' 'C'
```
.  
.  
Brunch 2
---
####if
```fsharp
let brunch1 x =
    if x = 1 then
        "hello"
    else
        "world"

1 |> brunch1 |> printfn "%A" // "hello"
```
####match 
```fsharp
let brunch2 x =
    match x with
    | 1  -> "hello"
    | _  -> "world"

1 |> brunch2 |> printfn "%A" // "hello"
```
