####to String
```fsharp
string 'a'
|> printfn "%A"
// “a"

'a'.ToString ()
|> printfn "%A"
// “a"

'a'
|> System.Convert.ToString
|> printfn "%A"
// “a"

"ABC9999EFG"
|> String.filter System.Char.IsLetter
|> printfn "%A"
// "ABCEFG"
```

####to Numeric
```fsharp
"9999"
|> System.Int32.Parse
|> printfn "%A"
// 9999

"9999"
|> System.Int32.TryParse
|> printfn "%A"
// (true, 9999)

"9999"
|> System.Convert.ToInt32
|> printfn "%A"
// 9999

"ABC9999EFG"
|> String.filter System.Char.IsDigit
|> System.Int32.Parse
|> printfn "%A"
// 9999
```

####List from String, String from List
```fsharp
"abc"
|> fun s -> [for c in s -> c]
|> printfn "%A"

// ['a'; 'b'; 'c']

['a';'b';'c']
|> List.toArray
|> System.String.Concat
|> printfn "%A"

// "abc"
```

####Tuple from List, List from Tuple
It's little dangarous!!
```fsharp
(1,'a',"abc")
|> tupleToList
|> Option.get
|> printfn "%A"
// [1; 'a'; "abc"]

['a';'b';'c']
|> List.map box
|> listToTuple
|> printfn "%A"
// ('a', 'b', 'c')

```
support function
```fsharp
// http://stackoverflow.com/questions/2920094/how-can-i-convert-between-f-list-and-f-tuple

let tupleToList t =
    if Microsoft.FSharp.Reflection.FSharpType.IsTuple(t.GetType())
        then Some (Microsoft.FSharp.Reflection.FSharpValue.GetTupleFields t |> Array.toList)
        else None

let listToTuple l =
    let l' = List.toArray l
    let types = l' |> Array.map (fun o -> o.GetType())
    let tupleType = Microsoft.FSharp.Reflection.FSharpType.MakeTupleType types
    Microsoft.FSharp.Reflection.FSharpValue.MakeTuple (l' , tupleType)
```

####Others
example for `Char`
```
.
├── Type of Char
     ├── Char              ‘a'
     ├── Char * int        (‘a’, 7)
     ├── list <Char>       [‘a’]
     ├── Char []           [|’a’|]
     ├── seq  <Char>       seq [‘a’]
     ├── Set  <Char>       Set [‘a’]
     ├── Map  <Char,int>   Map [(‘a’,7)]
     └── option <Char>     Some ‘a'
```
convert
```fsharp
'a'
|> id
|> printfn "%A"
// 'a'

'a'
|> fun x -> System.Tuple x
|> printfn "%A"
// ('a')

'a'
|> fun x -> [ x ]
|> printfn "%A"
// ['a']

'a'
|> fun x -> [| x |]
|> printfn "%A"
// [|'a'|]

'a'
|> fun x -> [ x ]
|> List.toSeq
|> printfn "%A"
// ['a'] Why?

'a'
|> fun x -> Set [ x ]
|> printfn "%A"
// set ['a']

'a'
|> fun x -> Map [ ( 1 , x ) ]
|> printfn "%A"
// map [(1, 'a')]

'a'
|> fun x -> Some x
|> printfn "%A"
// Some 'a'

```
