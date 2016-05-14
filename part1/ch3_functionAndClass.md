####Function
```fsharp
let say3 word = (word, word, word)

"I'm fine!" |> say3 |> printfn "%A"
// ("I'm fine!", "I'm fine!", "I'm fine!")
```

####Class
`:>` shows upcast.
Embody( method call ) needs upcast to interface class.
```fsharp
module Test_class =

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
    ( new Dog() :> IAnimal ).voice () |> printfn "%A"
    // "bow! bow!"

    ( new Cat() :> IAnimal ).voice () |> printfn "%A"
    // "mew! mew!"
```
