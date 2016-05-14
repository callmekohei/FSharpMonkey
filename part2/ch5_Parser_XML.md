####Load and Parse
Load form file  
```fsharp
// example Load
let filePath = @"./test.xml"
let xDoc = XDocument.Load filePath
```
Parse against Strings  
```
// example Parse
let url =  "http://rss.weather.yahoo.co.jp/rss/days/8210.xml"
let xDoc = XDocument.Parse ( Http.RequestString ( url ) )
```
####code
```fsharp
#r @"/usr/local/Cellar/mono/4.2.3.4/lib/mono/4.5/System.Xml.Linq.dll"
open System.Xml.Linq
module Test_Xml =
    
    let xdoc =
            """
            <member name="P:Microsoft.FSharp.Collections.FSharpList`1.Item(System.Int32)">
            <summary>Gets the element of the list at the given position.</summary>
            <remarks>Lists are represented as linked lists so this is an O(n) operation.</remarks>
            <param name="index">The index.</param>
            <returns>The value at the given index.</returns>
            </member>
            """
    let xname s = XName.Get s
    let xd = XDocument.Parse xdoc
```

####example

```fsharp
xd.Descendants (xname "param") |> printfn "%A"
// seq [<param name="index">The index.</param>]

xd.Descendants (xname "param")
|> Seq.map ( fun xe -> xe.Name )
|> printfn "%A"
// seq [param]

xd.Descendants (xname "param")
|> Seq.map ( fun xe -> xe.Attributes () )
|> printfn "%A"
// seq [seq [name="index"]]

xd.Root.Element(xname "param").Attribute(xname "name").Value
|> printfn "%A"
// "index"

xd.Descendants (xname "param")
|> Seq.map ( fun xe -> xe.Value )
|> printfn "%A"
// seq ["The index."]

```
