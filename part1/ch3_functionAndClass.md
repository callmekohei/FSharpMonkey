####Function
```fsharp
let say3 word = (word, word, word)

"I'm fine!" |> say3 |> printfn "%A"
// ("I'm fine!", "I'm fine!", "I'm fine!")
```

####Class
`:>` shows upcast.
```fsharp
module Test_class =

    type IAnimal  =
        abstract member voice : unit -> string

    type Dog () =
        interface IAnimal with
            member this.voice ()  = "bow! bow!"

    type Cat () =
        interface IAnimal with
            member this.voice () = "mew! mew!"

    let poti = ( new Dog() :> IAnimal )
    poti.voice () |> printfn "%A"
    // "bow! bow!"

    let tama = ( new Cat() :> IAnimal )
    tama.voice () |> printfn "%A"
    // "mew! mew!"
```
