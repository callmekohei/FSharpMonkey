# イベントについて

### イベントとは？

クラス内に定義されたデリゲート型のプロパティに対するシンタックスシュガー

### イベントの作成

イベントは `F# Event class` によって作成される

```fsharp
type Event<'T> =
  new            : unit -> Event<'T>
  member Trigger : arg:'T -> unit
  member Publish : IEvent<'T>

type Event<'Delegate,'Args (requires delegate and 'Delegate :> Delegate)> =
  new            : unit -> Event<'Delegate,'Args>
  member Trigger : sender:obj * args:'Args -> unit
  member Publish : IEvent<'Delegate,'Args>
```
##### sample01
`Event<'T>`
```fsharp
// creates event
let e = new Event<int>()
// create event handler
e.Publish
// add callbacks to event handler
|> Observable.add ( fun n -> stdout.WriteLine(n) )
// raising events
e.Trigger(1)

(*
    1
*)
```
##### sample02
`Event<'T>`  
`listeners`に`state`を渡す場合
```fsharp
open System

// event data
type ThresholdReachedEventArgs(number:int, dt:DateTime) =
    inherit System.EventArgs()
    
    member this.Threshold = number
    member this.TimeReached = dt

// Defining Events
type Counter() =
    // creates event
    let thresholdReached = new Event<ThresholdReachedEventArgs>()
    // exposed event handler
    member this.ThresholdReachedHandler = thresholdReached.Publish
    // invokes event handler
    member this.OnThresholdReached(a,b) = thresholdReached.Trigger( ThresholdReachedEventArgs(a,b))

// creates event sender
let c = new Counter()
// Adding Callbacks to Event Handlers
c.ThresholdReachedHandler |> Observable.add ( fun args -> stdout.WriteLine( (args.Threshold , args.TimeReached ) ))
// raising events
c.OnThresholdReached( 1, DateTime.Now )

(*
    (1, 7/17/2017 10:29:15 AM)
*)
```

##### Sample03
`Event<'Delegate,'Args>`  
他の言語(`C#,VB`）に`F# events`を見せる（露出）する場合
```fsharp
open System

// event data
type ThresholdReachedEventArgs<'a>( n:int, dt:DateTime) =
    inherit System.EventArgs()
    member this.Threshold = n
    member this.TimeReached = dt

// event delegate
type ThresholdReachedEventHandler<'a> = delegate of obj * ThresholdReachedEventArgs<'a> -> unit

// create events
let thresholdReached = new Event<ThresholdReachedEventHandler<obj>,ThresholdReachedEventArgs<'a>>()

// event hander
thresholdReached.Publish
// adding callbacks to event handler
|> Observable.add ( fun args -> stdout.WriteLine( ( args.Threshold, args.TimeReached  ) ))
// raising events
thresholdReached.Trigger(thresholdReached, ThresholdReachedEventArgs(1, System.DateTime.Now) )

(*
    (1, 7/17/2017 1:59:31 PM)
*)
```

### 用語
`Event listeners`
```
Event handler に登録された callbacks のこと
```
`Event handler`
```
Event().Publish されたもの
Observableモジュールの操作対象になる
```
`callbacks`
```
イベントが Trigger（発生・実行）したときに行う処理のこと
```
`Event data`
```
Eventに含まれるいろいろなデーター
```

# Basic Sample



### add
マネージメント・イベントハンドラを追加する
```fsharp
let e = Event<int>()

e.Publish
|> Observable.add ( fun n -> stdout.WriteLine(n) )
|> ignore

e.Trigger(1) // 1
```

### subscribe
アンマネージメント・イベントハンドラを追加する
```fsharp
open System.IO
let e = Event<int>()
e.Publish
|> Observable.subscribe ( fun n -> File.WriteAllText( "./abc.txt" , string n ) )
e.Trigger(1)
```

### map

イベントに対して何らかの処理を適用した`Observable`を作成する

```fsharp
let e = Event<int>()

e.Publish
|> Observable.map( fun n -> n + 100 )
|> Observable.add( fun n -> stdout.WriteLine(n) )
|> ignore

[1..5]
|> List.iter( fun n -> e.Trigger(n) )

(*
    101
    102
    103
    104
    105
*)
```

### filter

ある条件で１つの`observable`をつくる
```fsharp
let e = Event<int>()

e.Publish
|> Observable.filter( fun n -> n % 2 = 0)
|> Observable.add( fun n -> printfn "%A" n )
|> ignore

[1..10] |> List.map( fun n -> e.Trigger(n) )

(*
    2
    4
    6
    8
    10
*)
```

### partition
 
 ある条件で２つの`observable`をつくる

それぞれの`observable`に対して処理する場合に便利

```fsharp
let e = Event<int>()

e.Publish
|> Observable.partition( fun n -> n % 2 = 0)
|> fun (even,odd) ->
    even |> Observable.add( fun n -> printfn "even: %A" n )
    odd  |> Observable.add( fun n -> printfn "odd : %A" n )
|> ignore

[1..5] |> List.map( fun n -> e.Trigger(n) )

(*
    odd : 1
    even: 2
    odd : 3
    even: 4
    odd : 5
*)
```

### split

`partition`と同じようなもの？

```fsharp
let e = Event<int>()

e.Publish
|> Observable.split ( fun n -> if n % 2 = 0 then Choice1Of2(n) else Choice2Of2(n) )
|> fun (even,odd) ->
    even |> Observable.add( fun n -> printfn "even: %A" n )
    odd  |> Observable.add( fun n -> printfn "odd : %A" n )
|> ignore

[1..5]
|> List.iter( fun n -> e.Trigger(n) )

(*
    odd : 1
    even: 2
    odd : 3
    even: 4
    odd : 5
*)
```

### choose
filter + map
```fsharp
let e = Event<int>()

e.Publish
|> Observable.choose ( fun n -> if n % 2 = 0 then Some ( n + 100) else None )
|> Observable.add( fun n -> stdout.WriteLine(n) )
|> ignore

[1..10]
|> List.iter( fun n -> e.Trigger(n) )

(*
    102
    104
    106
    108
    110
*)
```

### scan
```fsharp
let e = Event<int>()

e.Publish
|> Observable.scan (+) 0
|> Observable.add( fun n -> stdout.WriteLine(n) )
|> ignore

[1..5]
|> List.iter( fun n -> e.Trigger(n) )

(*
    1
    3
    6
    10
    15
*)
```



### pariwise
となりあう要素同士をタプルでペアにした`Observable`を作成する
```fsharp
let e = Event<int>()

e.Publish
|> Observable.pairwise 
|> Observable.add ( fun tpl -> printfn "%A" tpl )
|> ignore

[1..5]
|> List.iter( fun n -> e.Trigger(n) )

(*
    (1, 2)
    (2, 3)
    (3, 4)
    (4, 5)
*)
```



### merge

２つのイベントを`or`で合成するして一つの`observable`にする

```fsharp
type OddEven () =
    let e = Event<int>()
    member this.Obs = e.Publish
    member this.Fire(n) = e.Trigger(n)

let even = OddEven()
let odd  = OddEven()

Observable.merge even.Obs odd.Obs
|> Observable.add ( fun n -> printfn "natural number: %A" n )

[1..5]
|> List.iter ( fun n -> if n % 2 = 0 then even.Fire(n) else odd.Fire(n) )

(*
    natural number: 1
    natural number: 2
    natural number: 3
    natural number: 4
    natural number: 5
*)
```

# Advanced Sample

### FileSystemWatcher
フォルダのファイルが、新規作成・削除・名前変更されたことを通知する
```fsharp

open System
open System.IO

let aaa () =
    let fsw = new FileSystemWatcher( "./fileSystemWater_test/", EnableRaisingEvents = true )
    [ fsw.Created; fsw.Deleted; Observable.map (fun x -> upcast x) fsw.Renamed; ] : IObservable<_> list
    |> List.reduce Observable.merge
    |> Observable.add ( fun x -> printfn "%A %A" x.ChangeType x.FullPath  )

let rec main s =
    match s with
    | "quit" -> ()
    | _      -> aaa ()
                main ( stdin.ReadLine() )

[<EntryPointAttribute>]
let entry arg =
    main ""
    0


(*
    $ mono abc.exe 
    Deleted "./fileSystemWater_test/bbb.txt"
    Created "./fileSystemWater_test/aaa.txt"
    Renamed "./fileSystemWater_test/bbb.txt"
*)
```

### OutputDataReceived
`child process`の標準出力を取得する

`parents program`
```fsharp
open System.Diagnostics

type Interaction (fp:string, args:string, wd:string) =
    
    // 子プロセスの起動パラメータを作成する
    let procStartInfo =
        ProcessStartInfo(
            FileName               = fp   ,
            Arguments              = args ,
            WorkingDirectory       = wd   ,
            RedirectStandardInput  = true , // 子プロセスの標準入力をリダイレクトする
            RedirectStandardOutput = true , // 子プロセスの標準出力をリダイレクトする
            RedirectStandardError  = true , // 子プロセスの標準エラーをリダイレクトする
            UseShellExecute        = false  // シェルを使用せず子プロセスを起動する(リダイレクトするために必要)
    )
    
    // 子プロセスをインスタンス化
    let ps = new Process(StartInfo = procStartInfo)

    // Set our event handler to asynchronously read the ps output.
    // リダイレクトした標準出力の内容を受信するイベントハンドラを設定する
    do ps.OutputDataReceived
       |> Event.add ( fun (args :DataReceivedEventArgs) ->
           stdout.WriteLine(args.Data) |> ignore )
    
    // 子プロセスを起動する
    do ps.Start() |> ignore
    // 子プロセスがアイドル状態になるまで無期限に待機
    do ps.WaitForInputIdle() |>ignore
    // Start the asynchronous read of the ps output stream.
    // 標準出力の非同期読み込みを開始する
    do ps.BeginOutputReadLine()

    // リダイレクトした子プロセスの標準入力にテキストを書き込む
    member this.Send(message:string) = ps.StandardInput.WriteLine(message)
    
    // 子プロセスを終了する
    member this.Quit() = ps.Kill()



let mono    = "/usr/local/Cellar/mono/4.8.1.0/bin/mono"
let args    = "/Users/kohei/Documents/childProcess.exe"
let workdir = "/Users/kohei/Documents/"
let replyer = Interaction(mono,args,workdir)

let rec main s =
    match s with
    | "quit" -> replyer.Quit()
    | _      -> replyer.Send(s)
                main ( stdin.ReadLine() )

[<EntryPointAttribute>]
let entry arg =
    main "hello"
    0
```
`child program`
```fsharp
let rec main s =
    match s with
    | "quit" -> ()
    | "init" -> stdout.WriteLine("HELLO from child process")
                main ( stdin.ReadLine() )
    | _      -> stdout.WriteLine( s + " --- from child process --- " )
                main ( stdin.ReadLine() )

[<EntryPointAttribute>]
let entry arg =
    main "init"
    0
```
