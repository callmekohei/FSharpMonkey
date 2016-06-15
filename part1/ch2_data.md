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

`\\` carage return in string
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
---

### Check type
`:?` is type checker with `box`.  
`tuple`, `record` needs `FSharp.Reflection.FSharpType`.
```fsharp

module TypeCheck =
    open FSharp.Reflection

    let str = "abc"
    let num = 9999
    let opt = Some 9999
    type Gender = Male | Female | Otokonoko
    let kohei = Male
    type PersonInfo = {Name : string; Age: int}
    let rcd = {Name = "kohei"; Age = 28}
    let tpl = ("callmekohei",28)
    let arr = [|1..5|]
    let lst = [1..5]
    let sq  = seq {1..5}
    let mp  = Map ["a",1; "b",2; "c",3]
    let st  = Set [1..10]

    (box str)   :? string          |> printfn "%A" // true
    (box num)   :? int             |> printfn "%A" // true
    (box opt)   :? option<int>     |> printfn "%A" // true
    (box kohei) :? Gender          |> printfn "%A" // true
 
    FSharpType.IsRecord (rcd.GetType ()) |> printfn "%A" // true
    FSharpType.IsTuple  (tpl.GetType ()) |> printfn "%A" // true

    (box arr)   :? array<int>      |> printfn "%A" // true
    (box lst)   :? list<int>       |> printfn "%A" // true
    (box sq)    :? seq<int>        |> printfn "%A" // true
    (box mp)    :? Map<string,int> |> printfn "%A" // true
    (box st)    :? Set<int>        |> printfn "%A" // true

```
