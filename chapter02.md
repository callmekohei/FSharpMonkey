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


F# Types をざっくりみてみる
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
     └── generic type            // 'a list, list<‘a>, Dictionary<'key, 'value>
```

FSharp Collections
---
コレクションは５種類あります。
```text
       
       Module Name    Type Name1    Type Name2
      ----------------------------------------
       List           list <_>      ‘a list
       Array          'T   []       ’T array
       seq            seq  <_>      ‘a seq
       Map            Map  <_,_>    ‘a ‘b Map
       Set            Set  <_>      ‘a Set
```

.NET Colletctions
---
```text
.
├── System.Collections.Generic
     ├── List<‘T>                       // mutable
     ├── HashSet<‘T>                    // mutable
     ├── Dictionary< ‘TKey , ‘TValue >  // mutable
     └── IDictionary<'Tkey , ’TValue >  // immutable (read only )
```





