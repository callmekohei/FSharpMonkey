###Fetch data
System.Net
```fsharp
module Test =

    let url = @"http://www.gutenberg.org/files/84/84.txt"

    let getHtml (url:string) =
        let req = System.Net.WebRequest.Create(url)
        let rsp = rsq.GetResponse ()
        use stream = rsp.GetResponseStream ()
        use reader = new StreamReader(stream)
        reader.ReadToEnd ()

    url |> getHtml |> printfn "%A"
```
Http.RequestString
```fsharp
let url = "https://gitter.im/fsugjp/public/archives/2015/06/01"
let HTML = HtmlDocument.Parse ( Http.RequestString url )
```
---

###Parse

HTML
```html
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>HTML-Title</title>
    </head>
    <body>

        <div class="test"></div>

        <div class="A" id="123" name="abc">
            <div class="B">
                <div class="C">callmekohei</div>
                <div class="D">@callmekohei</div>
                <a   class="E" title="Date">May 21 08:39</a>
            </div>
            <div class="F" name="Greeting"> Hello, World! </div>
        </div>

    </body>
</html>
```
basic code
```fsharp
open FSharp.Data

let element name ``class`` node =
        HtmlNode.name node = name && HtmlNode.attributeValue "class" node = ``class``

let html = HtmlDocument.Parse HTML

let nd = html |> HtmlDocument.descendants true ( element "div" "A" ) |> Seq.exactlyOne
```
result
```html
<div class="A" id="123" name="abc">
    <div class="B">
        <div class="C">callmekohei</div>
        <div class="D">@callmekohei</div>
        <a   class="E" title="Date">May 21 08:39</a>
    </div>
    <div class="F" name="Greeting"> Hello, World! </div>
</div>
```
.  
.  

---
###FSharp.Data.HtmlNode method

#####name
```fsaharp
nd |> fun x -> HtmlNode.name x |> printfn "%A" // "div"
```

#####Attribute
```fsharp
nd |> fun x -> HtmlNode.attribute "class" x |> fun x -> x.Name ()  |> printfn "%A" // "class"
nd |> fun x -> HtmlNode.attribute "class" x |> fun x -> x.Value () |> printfn "%A" // "A"
nd |> fun x -> HtmlNode.attributeValue "class" x                   |> printfn "%A" // "A"
nd |> fun x -> HtmlNode.attributes x |> Seq.map ( fun x -> HtmlAttribute.value  x )
    |> printfn "%A" // seq ["A"; "123"; "abc"]
nd |> fun x -> HtmlNode.tryGetAttribute "class" x                  |> printfn "%A" // Some FSharp.Data.HtmlAttribute
```

#####Has
```fsharp
nd |> fun x -> HtmlNode.hasAttribute "class" "A" x |> printfn "%A" // true
nd |> fun x -> HtmlNode.hasClass     "A"         x |> printfn "%A" // true
nd |> fun x -> HtmlNode.hasId        "123"       x |> printfn "%A" // true
nd |> fun x -> HtmlNode.hasName      "<test>"    x |> printfn "%A" // false
```

#####Inner Text
```fsharp
nd |> fun x -> HtmlNode.innerText x                |> printfn "%A" // "callmekohei@callmekoheiMay 21 08:39 Hello, World! "
nd |> fun x -> HtmlNode.innerTextExcluding ["a"] x |> printfn "%A" // "callmekohei@callmekohei Hello, World! "
```

#####Descendants
```fsharp
nd |> fun x -> HtmlNode.descendants                      true ( element "div" "F" ) x |> printfn "%A"
nd |> fun x -> HtmlNode.descendantsWithPath              true ( element "div" "F")  x |> printfn "%A"
nd |> fun x -> HtmlNode.descendantsNamed                 true ["a"] x                 |> printfn "%A"
nd |> fun x -> HtmlNode.descendantsNamedWithPath         true ["a"] x                 |> printfn "%A"

nd |> fun x -> HtmlNode.descendantsAndSelf               true ( element "div" "F" ) x |> printfn "%A"
nd |> fun x -> HtmlNode.descendantsAndSelfWithPath       true ( element "div" "F" ) x |> printfn "%A"
nd |> fun x -> HtmlNode.descendantsAndSelfNamed          true ["a"] x                 |> printfn "%A"
nd |> fun x -> HtmlNode.descendantsAndSelfNamedWithPath  true ["a"] x                 |> printfn "%A"
```

#####Elements
```fsharp
nd |> fun x -> HtmlNode.elements x                |> printfn "%A" 
nd |> fun x -> HtmlNode.elementsNamed ["a"] x     |> printfn "%A" // How to use?
```


