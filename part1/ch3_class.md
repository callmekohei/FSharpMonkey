
####Hierarchy of class
```text
. System
├── Object
     ├── String
     ├── ...
     ├── ...
     ├── ...
     └── ...
```

####Example of System.String class
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

####Interface
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

####Inheriatnce
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

####Override
```fsharp
module Exaple_Override =
    
    type Person ( name ) =
        override x.ToString () =
            "Name is " + name.ToString ()

    ( new Person("callmekohei") ).ToString () |> printfn "%A"
    // "Name is callmekohei"
```
