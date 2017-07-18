# F# cheatsheet

# Basic

### Comments
```fhsarp
(* block 
   comment *)

// line comment
```

### Pipeline
```fsharp
stdout.WriteLine("abc")

"abc" |> stdout.WriteLine
```

### stdout.WriteLine vs printfn
```fsharp
type ABC = { name:string }
printfn "%A" { name = "abc" }

// can not output!
// stdout.WriteLine( { nama = "abc" } )
```

### Printfn
```fsharp
"ABC" |> printfn "%A" // "ABC"  A is All.

true  |> printfn "%b" // true   b is bool.
'A'   |> printfn "%c" // A      c is char.
"ABC" |> printfn "%s" // ABC    s is string.
9999  |> printfn "%d" // 9999   d is digit.
1.23  |> printfn "%f" // 1.23   f is float.
```

### Identifiler
```fsharp
let callmekohei = 28
callmekohei |> stdout.WriteLine       // 28

let ``call me kohei`` = 28
``call me kohei`` |> stdout.WriteLine // 28
```

### Constant, variable and container
```fsharp
let lettersConstant = "Hello, World!"
lettersConstant |> stdout.WriteLine // Hello, World!

let mutable lettersVariable = "hello"
lettersVariable |> stdout.WriteLine // hello
lettersVariable <- "World!"
lettersVariable |> stdout.WriteLine // World!

let container = ref "hello"
!container      |> stdout.WriteLine // hello
container:= "world"
!container      |> stdout.WriteLine // world
```

### Loop
```fsharp
for c in "ABC" do
    c |> stdout.WriteLine
// A B C

let mutable i = 0
let str = "ABC"
while str.Length > i do
    str.[i] |> stdout.WriteLine
    i <- i + 1
// A B C
```

### Branch
```fsharp
let brunchIf x =
    if x = 1 then
        "hello"
    else
        "world"

1 |> brunchIf |> stdout.WriteLine // hello

let brunchMatch x =
    match x with
    | 1  -> "hello"
    | _  -> "world"

1 |> brunchMatch |> stdout.WriteLine // hello
```



### Null
```fsharp
// create
null                   |> printfn "%A"  // <null>
Unchecked.defaultof<_> |> printfn "%A"  // <null>
Array.zeroCreate<_> 1  |> printfn "%A"  // [|null|]

// check
isNull null |> stdout.WriteLine // True

// convert to None
Option.ofObj null |> fun x -> x.IsNone |> stdout.WriteLine // True
```

### Over flow
uy is byte unit
```fsharp
(+) 255uy 1uy |> stdout.WriteLine         // 0

Checked.(+) 255uy 1uy |> stdout.WriteLine // Arithmetic operation resulted in an overflow.
```

 

### Shadowing
```fsharp
let f () =
    let a, b = 1, 2
    let a, b = b, a
    a,b

f () |> printfn "%A" /// (2, 1) 

type A () =
    let a, b = 1, 2
    let a, b = b, a
    member this.Get = a, b

(A ()).Get |> printfn "%A" /// (2, 1)

/// unable!
(*
module A =
    let a,b = 1,2
    let a,b = b,a
    a,b
*)
```

### Short circuit
```fsharp
let a = 1

// not estimate a > 10
if a < 0 && a > 10 then
    stdout.WriteLine("hello")
else
    stdout.WriteLine("world")

/// world
```

<br>

# String

### verbatim string
```fsharp
" \\n \"abc\" "  |> stdout.WriteLine  // \n "abc"

@" \n ""abc"" "  |> stdout.WriteLine  // \n "abc"

""" \n "abc" """ |> stdout.WriteLine  // \n "abc"
```
### Concatenate
```fsharp
"A" + "B" |> stdout.WriteLine
// AB
```

### Carage return in string
```fsharp
 "ABC\
  DEF\
  GHI"
|> stdout.WriteLine
// ABCDEFGHI
```
<br>

# Data

### Tuple and Record
```fsharp
let giftBox = "apple", "banana", "cherry"
giftBox |> fun (a,b,c) -> stdout.WriteLine(a) // apple

type Fruits = { name : string }
let giftBox = { name = "apple"} , { name = "banana" } , { name = "cherry" }
giftBox |> fun (a,b,c) -> printfn "%A" a // {name = "apple";}
```

### Array, List, Seq, Map and Set
```fsharp
    Module Name              Type Name
    -----------    -------------------------------
    List           list <'a>     'a list
    Array          array<'a>     'a array    'a []
    seq            seq  <'a>     'a seq
    Map            Map  <'a,'b>  'a 'b Map
    Set            Set  <'b>     'a Set

// List
[1..5] |> printfn "%A" // [1; 2; 3; 4; 5]

// Array (mutable)
[|1..5|] |> printfn "%A" // [|1; 2; 3; 4; 5|]

// seq ( lazy estimate )
seq {1..5} |> printfn "%A" // seq [1; 2; 3; 4; ...]

// Map ( List of Tuple )
Map.ofList["a",1; "b",2; "c",3] |> printfn "%A" // map [("a", 1); ("b", 2); ("c", 3)]

// Set ( has unique value )
Set [1..10] |> printfn "%A" // set [1; 2; 3; 4; 5; 6; 7; 8; 9; ...] 
```
<br>

# Function

### 2-ways expression of function
```fsharp
let twice x = x * 2
5 |> twice              |> printfn "%A" // 10
5 |> ( fun x -> x * 2 ) |> printfn "%A" // 10
```

### Return value
```fsharp
let bookShelf x = x 
let Trash x = x |> ignore

"paper" |> bookShelf |> printfn "%A" // "paper"
"paper" |> Trash     |> printfn "%A" // <null>
```

### Byval and ByRef
```fsharp
// byval
let x = 5      // x is label
let twice a = a * 2

x |> twice |> printfn "%A"


// byref
let y = ref 5  // y is container
let twice_ref ( b:int ref ) =
    b := !b * 2

y  |> twice_ref
!y |> printfn "%A"

```

### Recursive functions
```fsharp
let rec say word i =
    match i with
    | 0 -> ""
    | _ -> printfn "%A" word
           say word (i-1)

("callmekohei",3) ||> say
// "callmekohei"
// "callmekohei"
// "callmekohei"
```

### Higher-order functions on collections
```fsharp
[1;2;3] |> List.map ( fun n -> n * 2 ) |> printfn "%A"
// [2; 4; 6]
```
<br>

# Class

### Hierarchy of class
```text
. System
├── Object
     ├── String
     ├── ...
     ├── ...
     ├── ...
     └── ...
```

### Example of System.String class
```fsharp
module example_Object =

    let str = "callmekohei"

    // str is string object from System.String class.
    str.GetType ()     |> printfn "%A" // System.String

    // str has method of System.String class
    str.ToUpper ()     |> printfn "%A" // "CALLMEKOHEI"
    str.Length         |> printfn "%A" // 11
    str.GetHashCode () |> printfn "%A" // 1656266288
```

### Interface
`:>` shows upcast.  
Embody( method call ) needs to upcast.
```fsharp
module Example_interface =

    // difine
    type IAnimal  =
        abstract member voice : unit -> string
    
    // impliment
    type Dog () =
        interface IAnimal with
            member this.voice ()  = "bow! bow!"

    type Cat () =
        interface IAnimal with
            member this.voice () = "mew! mew!"
    
    // embody ( call )
    ( new Dog() :> IAnimal ).voice () |> printfn "%A" // "bow! bow!"
    ( new Cat() :> IAnimal ).voice () |> printfn "%A" // "mew! mew!"
```

### Inheriatnce
```fsharp
module Example_Inheritance =

    // base class
    type Animal () =
        member x.voice = "Uuuu.., Uuuu..."

    // delivetive class
    type Dog () =
        inherit Animal ()
        member x.voice ()  = base.voice
        member x.bark () = "bow! bow!"

    type Cat () =
        inherit Animal ()
        member x.voice () = base.voice
        member x.mew () = "mew! mew!"

    // embody ( call )
    (new Dog()).voice () |> printfn "%A" // "Uuuu.., Uuuu..."
    (new Dog()).bark ()  |> printfn "%A" // "bow! bow!"
    (new Cat()).voice () |> printfn "%A" // "Uuuu.., Uuuu..."
    (new Cat()).mew ()   |> printfn "%A" // "mew! mew!"
```

### Override
```fsharp
module Exaple_Override =
    
    type Person ( name ) =
        override x.ToString () =
            "Name is " + name.ToString ()

    ( new Person("callmekohei") ).ToString () |> printfn "%A"
    // "Name is callmekohei"
```
<br>

# Computation

```fsharp
module sample00 =

    // 通常の式は return はひつようない
    // legular exp :     let x = 99 in x 

    // computation exp. は return が必要
    // computation exp : let x = 99 in return x 

    let x = 99 in x |> printfn "%A" 
    // 99
```

### Return
```fsharp
module sample01 = 
    
    // return される式（この場合 n ）は Return のパラメーターになる
    
    type myBuilder () =
        member this.Return x = x + 100

    let myBld = new myBuilder()
    
    myBld{ let n = 99 in return n } |> printfn "%A" 
    // 199

module sample02 = 

    type myBuilder () =
        member this.Return x = fun () -> x // 関数でつつみこむ（closure)

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n } |> printfn "%A"
    // <fun:Return@28>
    
    () |> myBld { let n = 99 in return n } |> printfn "%A"
    // 99
```

### Return!
```fsharp
module sample08 = 

   // computation exp.のなかに return! をいれると ReturnFrom に
   // computation exp を渡せます
    
    type myBuilder () =
        member this.ReturnFrom x = x + 500
        member this.Return x     = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n }  |> printfn "%A" 
    // 299

    myBld { let n = 99 in return! n } |> printfn "%A"
    // 599
```



### Bind
```fsharp
module sample05 = 

    // computation exp.のなかに let! をいれると Bind にタプルとしてわたされる
    // tuple = ( 99, (fun x -> x + 200))
    
    type myBuilder () =
        member this.Bind (x, _) = x + 100
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n }  |> printfn "%A" 
    // 299

    myBld { let! n = 99 in return n } |> printfn "%A"
    // 199

module sample06 = 

    // Bind の tuple の第２パラメーターを利用すると式を継続できます
    // この場合式は Return を示してます

    type myBuilder () =
        member this.Bind (x, f) = f (x + 100)
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n }  |> printfn "%A" 
    // 299

    myBld { let! n = 99 in return n } |> printfn "%A"
    // 399
```

### Zero
```fsharp
module sample07 = 

    // Zero は if の else 側がない場合に実行される

    type myBuilder () =
        member this.Zero () = 500
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let x = 99
            if x = 0 then return 0} |> printfn "%A" 
    
    // 500
```



### Delay
```fsharp
module sample04 = 

    // Delay は 最初に実行されます（初期設定みたいな感じ）
    // Delayにわたされるときはクロージャになります

    type myBuilder () =
        member this.Delay  wrapedFunc =  wrapedFunc () + 100
        member this.Return x = x + 200

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n } |> printfn "%A" 
    // 399
```


### Run
```fsharp
module sample03 = 

    // Run をメソッドに加えると computaion exp. が実行される

    type myBuilder () =
        member this.Return x = fun () -> x
        member this.Run f = f () + 100

    let myBld = new myBuilder()
    
    myBld { let n = 99 in return n } |> printfn "%A"
    // 199
```


### Combine
```fsharp
module sample09 = 

    // Combine は computation exp.を束ねます（combine) 

    type myBuilder () =
        member this.Combine (a,b) =
            printfn "combine"        
            a@b
        member this.Return x =
            printfn "return"
            x
        member this.Delay f = f ()

    let myBld = new myBuilder()

    myBld { let x = [1..3] in return x
            let y = [5..8] in return y} |> printfn "%A"

    //  return
    //  return
    //  combine
    //  [1; 2; 3; 5; 6; 7; 8]

module sample10 = 

    type myBuilder () =
        member this.Combine (a,b) =
            printfn "combine %A %A" a b      
            a@b
        member this.Return x =
            printfn "return"
            x
        member this.Delay(f) = f()

    let myBld = new myBuilder()

    myBld { let x = [1;2] in return x
            let y = [3;4] in return y
            let z = [5;6] in return z } |> printfn "%A" 
    
    //return
    //return
    //return
    //combine [3; 4] [5; 6]
    //combine [1; 2] [3; 4; 5; 6]
    //[1; 2; 3; 4; 5; 6]
```

