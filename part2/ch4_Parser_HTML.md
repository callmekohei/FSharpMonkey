####when you cannot fetch data
use `System.Net`
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

####fetch as Strings
```fsharp
use htmlDoc = Http.RequestString url

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

####fetch as `seq<HtmlNode>`
```
use htmlNode = HtmlDocument.Load  ( url )
use htmlNode = HtmlDocument.Parse ( strings )
```
.  
.  

####titleタグ
```fsharp
htmlDoc.Descendants ["title"] |> printfn "%A"
// seq [<title>サンプル</title>]

htmlDoc.Descendants ["title"]
|> Seq.map (fun xe -> xe.InnerText() )
|> printfn "%A"
// seq ["サンプル"]
```

####meta タグ
```fsharp
htmlDoc.Descendants ["meta"] |> printfn "%A"
// seq [<meta charset="UTF-8" />]

let charset =
    htmlDoc.Descendants ["meta"]
    |> Seq.choose (fun x ->
        x.TryGetAttribute("charset")
        |> Option.map (fun a -> x.InnerText(), a.Value()    )
    )
// seq [("", "UTF-8")]
```
####ul タグ
```fsharp
#r @"./packages/FSharp.Data/lib/net40/FSharp.Data.dll"
open FSharp.Data
open System

module Test =

    let htmlText =
        """
        <ul class="foods">
            <li>apple</li>
            <li>orange</li>
            <li>cherry</li>
        </ul>
        """
    let htmlNode = ( HtmlDocument.Parse htmlText ).Descendants ( ["ul"] )

    let getName ( category:string , position:int) =

        htmlNode
        |> Seq.pick ( fun x ->
            if x.HasClass category then
                Some ( Seq.item position ( x.Elements () )
                        |> (fun xe -> xe.InnerText () ) )
            else
                None )

    getName ( "foods",1 ) |> printfn "%A"
```


