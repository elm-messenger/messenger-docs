---
sidebar_position: 3
---

# Initial Asset Loading Scene

:::warning
You may want to use the [asset loading GC](../advanced/gc.md) instead. It automatically handles the asset loading scene for you.
:::

Users may want to have an asset loading scene when the game starts.

When Messenger is loading assets, `update` of the initial scene will not be called. All the user input and events are ignored. However, `view` will be called. `globalTime` and `currentTimeStamp` will be updated but `sceneStartTime` will not be updated.

Moreover, users can get the number of loaded assets by using the `loadedResourceNum` function. Then you can compare it with `resourceNum resource`.

An example:

```elm
import REGL.BuiltinPrograms as P
startText : GlobalData UserData -> Renderable
startText gd =
    let
        loaded =
            loadedResourceNum gd

        total =
            resourceNum resources

        progress =
            String.slice 0 4 <| String.fromFloat (toFloat loaded / toFloat total * 100)

        text =
            if loaded /= total then
                "Loading... " ++ progress ++ "%"

            else
                "Click to start"
    in
    group []
        [ P.textboxCentered ( 960, 900 ) 60 text "Arial" Color.black 
        ]
```

