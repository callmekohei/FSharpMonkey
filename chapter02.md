About Type
---
`F#`の型に関しては２つの側面から見る必要があるかもしれません。  
ひとつは`F# types`、もうひとつは`F# Collection Types`です。  
またあわせて`.NET`の型である`System.Collections.Generic`も知っておく必要があります。  
```text
理解しておくべき３種類の型
1: FSharp types
2: FSharp Collection Types (Microsoft.FSharp.Collections)
3: System.Collections.Generic
```


F# Types
---
実際には１５種類に分類されてます。  
詳細は下記アドレスを参照  
https://msdn.microsoft.com/en-us/library/dd233230.aspx
```text
.
├── F# Types
     ├── primitive type          // int, string
     ├── array                   // int[], array<int>, int array
     ├── tuple                   // (1,'b',3)
     └── generic type            // 'a list, list<'a>, Dictionary<'key, 'value>
```

FSharp Collections
---
コレクションは５種類あります。
```text
       
       Module Name    Type Name1    Type Name2    Type Name3
      ------------------------------------------------------
       List           list  <_>      'a list
       Array          array <_>      ’a array    'a []  
       seq            seq   <_>      'a seq
       Map            Map   <_,_>    'a 'b Map
       Set            Set   <_>      'a Set
```

.NET Colletctions
---
```text
.
├── System.Collections.Generic
     ├── List<'T>                       // mutable
     ├── HashSet<'T>                    // mutable
     ├── Dictionary< 'TKey , 'TValue >  // mutable
     └── IDictionary<'Tkey , ’TValue >  // immutable (read only )
```

ポイント！
---
####list<'T> と List<'T> は別物！
```fsharp
// list<'T>

[1;2;3]
|> fun x -> x.GetType ()
|> printfn "%A"
// Microsoft.FSharp.Collections.FSharpList`1[System.Int32]


// List<'T>

let arr = ResizeArray<_>()
for v in [1..3] do
    arr.Add v
arr
|> fun x -> x.GetType ()
|> printfn "%A"
// System.Collections.Generic.List`1[System.Int32]
```

Function Type における Signature
---
たとえば`List.map`の`syntax`は下記のようになってます。
```fsharp
// Signature:
List.map : ('T -> 'U) -> 'T list -> 'U list

// Usage:
List.map mapping list
```
`signature`を読むには`usage`をつかむのがポイントかもしれません。
```text
//example usage
source       seq<'T>
action       ('T -> unit)
mapping      ('T -> 'U)
predicate    ('T -> bool)
folder       ('State -> 'T -> 'State)
state        state
choose       ('T -> 'U option)
projection   ('T -> 'Key)
```



