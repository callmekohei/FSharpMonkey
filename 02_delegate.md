# Delegateとは？
異なる複数の関数・メソッドを同じ形式で呼び出すしくみ

戻り値と引数が同じ関数・メソッドならデリゲートを通じてよびだせる

### デリゲートの定義 ( define )
```fsharp
// Action
// 返り値が必要ない処理に使用する
System.Action

// Func
// 返り値が必要な処理に使用する
System.Func
OptimizedClosures.FSharpFunc<_>.Adapt

// custom
// イベント発生時に追加の情報が必要な場合使用する
type MyDelegate = delegate of obj * EventArgs -> unit
```



### サンプル
Action
```fsharp
open System
// アクションデリゲートを作成する
let dlg1 = Action( fun _ -> stdout.WriteLine("hello") )
let dlg2 = Action( fun _ -> stdout.WriteLine("world") )

// コンバインしてみる（ invoke が一回ですむ ）
Action.Combine( dlg1, dlg2 )
|> fun x -> x :?> Action
|> fun x -> x.Invoke()

// hello
// world
```

Func
```fsharp
open System
// ファンクデリゲートを作成する
let dlg1 = Func<_,_,_>( fun x y -> x + y )
let dlg2 = Func<_,_,_>( fun x y -> x - y )
let dlg3 = Func<_,_,_>( fun x y -> x * y )

// コンバインしてみる（あまり意味はないかも・・・）
Func<_,_,_>.Combine( dlg1, dlg2, dlg3 )
|> fun x -> x.GetInvocationList()
|> Array.map ( fun dlg -> dlg :?> Func<int,int,int> )
|> fun ary -> 
    ( ary , [|(1,2);(3,4);(5,6)|] ) ||> Array.map2 ( fun dlg param -> dlg.Invoke param ) 
|> printfn "%A"
// [|3; -1; 30|]
```

FSharpFunc

`Func`と`FSharpFucn`の違いはパイプラインで流せるかどうか
```fsharp
open FSharp.Core.OptimizedClosures
FSharpFunc<int, int, int>.Adapt (fun x y -> x + y)
|> fun dlg -> dlg.Invoke(1)
|> fun f -> f 2
|> stdout.WriteLine // 3
```


custom
```fsharp
open System

// イベント起動時に追加の情報を送る場合
// EventArgs派生クラスが必要
type MyEventArgs<'a>( message: string) =
    // EventArgsから継承
    inherit System.EventArgs()
    
    // 現在時刻を変数にバインド
    let (|Date|) (dt:DateTime) = (dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second)
    let y,m,d,h,mi,s = (|Date|) System.DateTime.Now
    
    // 各プロパティに変数をバインド
    member this.Message = message
    member this.Y = y ; member this.M  = m  ; member this.D = d
    member this.H = h ; member this.Mi = mi ; member this.S = s
    
// カスタムデリゲートを作成
type MyDlg<'a> = delegate of obj * MyEventArgs<'a> -> unit

type Printer() =
    // イベントクラスを実体化
    let e = new Event<MyDlg<'a>,MyEventArgs<'a>>()
    
    // イベントを実体化
    member this.Changed = e.Publish
    
    // イベント発生のトリガー
    member this.output(s) = e.Trigger(this, MyEventArgs(s) )

let printer = Printer()
// イベントハンドラの登録
printer.Changed |> Observable.add( fun x -> printfn "%s %A/%A/%A %0A:%0A:%0A" x.Message x.Y x.M x.D x.H x.Mi x.S )
// イベント実行
printer.output("hello") // hello 2017/7/13 16:48:55
```
