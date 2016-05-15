####Pickup numbers
```fsharp
"abc123def"
|> String.filter System.Char.IsDigit
|> printfn "%A"
// "123"
```

####sort
```fsharp
let lst = [
    "963,2015/04/30,04,23,29,31,32,35"
    "1,2000/10/05,02,08,10,13,27,30"
    "1066,2016/04/28,02,11,29,36,40,42"
    "1065,2016/04/25,11,13,27,37,41,42" ]

lst
|> Seq.sortBy ( fun s -> System.Int32.Parse ( s.Split(',').[0] ) )
|> printfn "%A"

// seq [ "1,2000/10/05,02,08,10,13,27,30"
//       "963,2015/04/30,04,23,29,31,32,35"
//       "1065,2016/04/25,11,13,27,37,41,42"
//       "1066,2016/04/28,02,11,29,36,40,42" ]
```

####String Method
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
