####Fetch data
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
let url = "http://www.aozora.gr.jp/index_pages/person81.html"
let text = Http.RequestString (
                url
                , headers = [ UserAgent
                    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4)
                    AppleWebKit/537.36 (KHTML, like Gecko)
                    Chrome/49.0.2623.87 Safari/537.36" ]
                , responseEncodingOverride = "utf-8" )

let htmlDoc = HtmlDocument.Parse text
```
---

####Parse

HTML
```html
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>HTML-Title</title>
    </head>
    <body>

        <div class="test"></div>

        <div class="chat-item__content">
            <div class="chat-item__details">
                <div class="chat-item__from js-chat-item-from">callmekohei</div>
                <div class="chat-item__username js-chat-item-from">@callmekohei</div>
                <a class="chat-item__time js-chat-time" href="https://gitter.im/fsugjp/public?at=574842e5454cb2be094f813c">2016/05/26</a>
            </div>
            <div class="chat-item__text js-chat-item-text">Hello, world</div>
        </div>

    </body>
</html>
```
.  
.  
Library
```
open FSharp.Data
```
.  
.  

util function
```fsharp
let element name ``class`` node =
        HtmlNode.name node = name && HtmlNode.attributeValue "class" node = ``class``
```
.  
.  
parse strings ( Parse is strings. Load is URL or File )
```
let HTML = HtmlDocument.Parse str
```
.  
.  
get target - tag
```fsharp
let targetTag = 
    HTML 
    |> HtmlDocument.descendants false ( element "div" "chat-item__content" ) 
    |> Seq.exactlyOne

targetTag |> printfn "%A"
```
result
```html
<div class="chat-item__content">
    <div class="chat-item__details">
    <div class="chat-item__from js-chat-item-from">callmekohei</div><div class="chat-item__username js-chat-item-from">@callmekohei</div><a class="chat-item__time js-chat-time" href="https://gitter.im/fsugjp/public?at=574842e5454cb2be094f813c">2016/05/26</a>
    </div><div class="chat-item__text js-chat-item-text">Hello, world</div>
</div>
```
.  
.  
structure of target div tag
```fsharp
targetTag
|> HtmlNode.descendantsAndSelf true ( fun x -> true )
|> Seq.map ( fun x -> HtmlNode.name x )
|> Seq.filter ( fun x -> ( Seq.isEmpty >> not ) x )
|> Seq.toList
|> printfn "%A"
```
result
```
["div"; "div"; "div"; "div"; "a"; "div"]
```
.  
.  
a-tag's link address
```fsharp
targetTag
|> HtmlNode.descendants true ( element "a" "chat-item__time js-chat-time" )
|> Seq.map ( fun x -> HtmlNode.attributeValue "href" x )
|> printfn "%A"
```
result
```fsharp
seq ["https://gitter.im/fsugjp/public?at=574842e5454cb2be094f813c"]
```
.  
.  
5th div tag's inner text
```fsharp
targetTag
|> HtmlNode.descendants true ( element "div" "chat-item__text js-chat-item-text" )
|> Seq.map ( fun x -> HtmlNode.innerText x )
|> printfn "%A"
```
result
```fsharp
seq ["Hello, world"]
```

