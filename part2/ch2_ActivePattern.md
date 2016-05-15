####Single Total
```fsharp
// 実践F# page293
let (|Date|) ( dt: System.DateTime ) =
    (dt.Year, dt.Month, dt.Day)

(|Date|) System.DateTime.Now |> printfn "%A" // (2016, 5, 15)
```
```fsharp
// http://www.devjoy.com/2014/08/active-patterns-single-total-a/
let (|UpperCaseCount|) (str: string) =
    str.ToCharArray()
    |> Array.filter (fun c -> c.ToString() = c.ToString().ToUpper())
    |> Array.length

(|UpperCaseCount|) "CallMeKohei" |> printfn "%A" // 3
```
```fsharp
// http://luketopia.net/2014/09/11/interesting-active-patterns/
let (|NonEmptyString|) value =
    if System.String.IsNullOrEmpty value
    then failwith "String must be non-empty."
    else value

let sayHello (NonEmptyString name) =
    printfn "Hello, %s." name

"callmekohei" |> sayHello // Hello, callmekohei.
```
```fsharp
// http://qiita.com/adacola/items/0d5f6518f1bd01143725
let (|NaturalNumber|) comparand value =
    if value < comparand
    then failwith "NaturalNumber must be greater than zero!"
    else value

99 |> (|NaturalNumber|) 0 |> printfn "%A" // 99
```
