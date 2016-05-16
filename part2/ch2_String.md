####sort
```fsharp
[ "963"; "1"; "1066"; "1065" ]
|> Seq.sortBy ( fun s -> System.Int32.Parse s )
|> printfn "%A"
// seq ["1"; "963"; "1065"; "1066"]
```

####String function ( Microsoft.FSharp.Core.String )
```fsharp
module Test_String =

    let str = "callmekohei123"

    String.init 10 ( fun n -> string (n % 2) )                 |> printfn "%A" // "0101010101"
    String.replicate 5 "---"                                   |> printfn "%A" // "---------------"

    str |> String.length                                       |> printfn "%A" // 14
    str |> String.exists ( fun s -> s = 'm' )                  |> printfn "%A" // true
    str |> String.forall ( fun x -> System.Char.IsDigit x )    |> printfn "%A" // false

    str |> String.collect ( fun c -> string c + "-" )          |> printfn "%A" // "c-a-l-l-m-e-k-o-h-e-i-"

    ["call";"me";"kohei"]  |> String.concat "-"                |> printfn "%A" // "call-me-kohei"

    str |> String.filter System.Char.IsDigit                   |> printfn "%A" // "123"
    str |> String.map System.Char.ToUpper                      |> printfn "%A" // "CALLMEKOHEI123"
    str |> String.mapi ( fun i c -> if i < 4 then c else '*' ) |> printfn "%A" // "call*******"

    str |> String.iter  ( printfn "%A" )
    str |> String.iteri ( printfn "%A %A" )

```

####String Method 2 ( .NET Framework library)
```fsharp
let str:string = "Call-Me-Kohei"

// seek
str.GetType ()              |> printfn "%A" // System.String
str.GetTypeCode ()          |> printfn "%A" // String
str.Length                  |> printfn "%A" // 13
str.StartsWith "C"          |> printfn "%A" // true
str.EndsWith "i"            |> printfn "%A" // true
str.IndexOf "M"             |> printfn "%A" // 5
str.IndexOfAny [|'i';'o'|]  |> printfn "%A" // 9
str.Chars 5                 |> printfn "%A" // 'M'
str.Contains "Me"           |> printfn "%A" // true

// process
str.Insert (4, "-")         |> printfn "%A" // "Call--Me-Kohei"
str.Remove 7                |> printfn "%A" // "Call-Me"
str.Replace ( "Me", "You" ) |> printfn "%A" // "Call-You-Kohei"
str.Split ( '-' )           |> printfn "%A" // [|"Call"; "Me"; "Kohei"|]
str.ToCharArray ()          |> printfn "%A" // [|'C'; 'a'; 'l'; 'l'; '-'; 'M'; 'e'; '-'; 'K'; 'o'; 'h'; 'e'; 'i'|]

str.Substring ( 5, 2 )      |> printfn "%A" // "Me"
str.[5..6]                  |> printfn "%A" // "Me"

str.ToUpper ()              |> printfn "%A" // "CALL-ME-KOHEI"
str.ToLower ()              |> printfn "%A" // "call-me-kohei"
str.ToUpperInvariant ()     |> printfn "%A" // "CALL-ME-KOHEI"
str.ToLowerInvariant ()     |> printfn "%A" // "call-me-kohei"

str.Trim ()                 |> printfn "%A" // "Call-Me-Kohei"
str.TrimStart ( [|'C'|] )   |> printfn "%A" // "all-Me-Kohei"
str.TrimEnd ( [|'i'|] )     |> printfn "%A" // "Call-Me-Kohe"

str.PadLeft 20              |> printfn "%A" // "       Call-Me-Kohei"
str.PadRight 20             |> printfn "%A" // "Call-Me-Kohei       "
```
