Chapter2 Data
---
####Strings  
`+` is concatenate.
```fsharp
"A" + "B"
|> printfn "%A"
// "AB"
```

`@` escapes control char.
```fsharp
@"https://www.google.co.jp/"
|> printfn "%A"
// https://www.google.co.jp
```

carage return in string
```
 "ABC\
  DEF\
  GHI"
|> printfn "%A"
// "ABCDEFGHI"
```

####Int
```fsharp
1 + 2
|> printfn "%d"
// 3

1.3 + 2.4
|> printfn "%f"
// 3.700000
```

####Collection 5
```text
.
├── Microsoft.FSharp.Collections
     ├── list <_>    ‘a list
     ├── ’T   [ ]    ’T array
     ├── seq  <_>    ‘a seq
     ├── Map  <_,_>  ‘a ‘b Map
     └── Set  <_>    ‘a Set
```

Array (mutable)
```fsharp
[|1..5|] |> printfn "%A"
// [|1; 2; 3; 4; 5|]
```

List and seq
```fsharp
[1..5] |> printfn "%A"
// [1; 2; 3; 4; 5]

seq {1..5} |> printfn "%A"
// seq [1; 2; 3; 4; ...]
```
Map ( is list of tuple )
```fsharp
Map.ofList["a",1; "b",2; "c",3] |> printfn "%A"
// map [("a", 1); ("b", 2); ("c", 3)]
```

Set ( unique value )
```fsharp
Set [1..10] |> printfn "%A"
// set [1; 2; 3; 4; 5; 6; 7; 8; 9; ...] 
```

####Option ( takes Some or None )
```fsharp
Some "ABC" |> printfn "%A"
// Some "ABC"

None |> printfn "%A"
// <null>
```

####DU
DU is familiar with Set data.
```fsharp
type Gender = Male | Female | Otokonoko
let myGender = Male |> printfn "%A"
// Male
```

####tuple and record
```fsharp
// tuple
(1,2)     ||>  printfn "%d %d"      // 1 2

// record
type I = {Name : string; Age: int}
{Name = "kohei"; Age = 28}.Name|> printfn "%s"
// kohei
```

####Expression
example for `char`
```
      Type name         Value or Express
      --------------    ----------------
      Char              ‘a'
      Char * int        (‘a’, 7)
      list <Char>       [‘a’]
      Char []           [|’a’|]
      seq  <Char>       seq [‘a’]
      Set  <Char>       Set [‘a’]
      Map  <Char,int>   Map [(‘a’,7)]
      option <Char>     Some ‘a'
```

####Patern match
```fsharp
let f x =
    match x with
    | 1 -> "one"
    | 2 -> "two"
    | _ -> "more"

for i in 1..4 do
    (i, f i) ||> printfn "%d:  %s"

// 1:  one
// 2:  two
// 3:  more
// 4:  more
```
