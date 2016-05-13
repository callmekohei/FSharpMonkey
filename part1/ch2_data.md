Chapter2 Data
---

F# data is ...
```text
.
├── FSharp data ( simple view )
     ├── Strings          // "abc"
     ├── Int              // 1,2,3 ...
     ├── Option           // Some "a", None
     ├── Du               // Male | Female
     ├── Record           // { name; "callmekohei", age; 28 }
     ├── Tuple            // ( "callmekohei", 28 )
     └── Collection 5
```

####Strings  
`+` is concatenate.
```fsharp
"A" + "B" |> printfn "%A"
// "AB"
```

`@` escapes control char.
```fsharp
@"https://www.google.co.jp/" |> printfn "%A"
// https://www.google.co.jp
```

`\` carage return in string
```fsharp
 "ABC\
  DEF\
  GHI"
|> printfn "%A"
// "ABCDEFGHI"
```

####Int
```fsharp
1 + 2 |> printfn "%d"
// 3

1.3 + 2.4 |> printfn "%f"
// 3.700000
```

####Option ( takes only Some or None )
```fsharp
Some "ABC" |> printfn "%A"
// Some "ABC"

None |> printfn "%A"
// <null>
```

####DU ( has separated data type )
```fsharp
type Gender = Male | Female | Otokonoko

Gender.Male |> printfn "%A"
// Male
```

####Record ( has index for value )
```fsharp
type PersonInfo = {Name : string; Age: int}

{Name = "kohei"; Age = 28} |> printfn "%A"
// {Name = "kohei"; Age = 28;}
```

####Tuple ( is simple Record )
```fsharp
("callmekohei",28) |> printfn "%A"
// ("callmekohei", 28)
```

####Collection 5
```text
    Module Name              Type Name
    -----------    -------------------------------
    List           list <'a>     'a list
    Array          array<'a>     'a array    'a []
    seq            seq  <'a>     'a seq
    Map            Map  <'a,'b>  'a 'b Map
    Set            Set  <'b>     'a Set
```

List
```fsharp
[1..5] |> printfn "%A"
// [1; 2; 3; 4; 5]
```

Array (mutable)
```fsharp
[|1..5|] |> printfn "%A"
// [|1; 2; 3; 4; 5|]
```

seq
```fsharp
seq {1..5} |> printfn "%A"
// seq [1; 2; 3; 4; ...]
```
Map ( is List of Tuple )
```fsharp
Map.ofList["a",1; "b",2; "c",3] |> printfn "%A"
// map [("a", 1); ("b", 2); ("c", 3)]
```

Set ( has unique value )
```fsharp
Set [1..10] |> printfn "%A"
// set [1; 2; 3; 4; 5; 6; 7; 8; 9; ...] 
```
.  
.  
.  
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

####Pattern match
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
