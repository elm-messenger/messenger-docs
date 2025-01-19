---
sidebar_position: 6
---

# Message Passing

Recall the `update` function in `ConcreteGeneralModel`.

```elm
update : env -> event -> data -> bdata -> ( ( data, bdata ), List (Msg tar msg sommsg), ( env, Bool ) )
```

However, layers generally do not need `bdata`, so Messenger provides a `ConcreteLayer` type that hides `bdata` (making it a `()`):

```elm
type alias ConcreteLayer data cdata userdata tar msg scenemsg =
    { init : LayerInit cdata userdata msg data
    , update : LayerUpdate cdata userdata tar msg scenemsg data
    , updaterec : LayerUpdateRec cdata userdata tar msg scenemsg data
    , view : LayerView cdata userdata data
    , matcher : Matcher data tar
    }
```

Here `LayerUpdate` is defined by:

```elm
type alias LayerUpdate cdata userdata tar msg scenemsg data =
    Env cdata userdata -> WorldEvent -> data -> ( data, List (Msg tar msg (SceneOutputMsg scenemsg userdata)), ( Env cdata userdata, Bool ) )
```

`LayerUpdateRec` is defined by:

```elm
type alias LayerUpdateRec cdata userdata tar msg scenemsg data =
    Env cdata userdata -> msg -> data -> ( data, List (Msg tar msg (SceneOutputMsg scenemsg userdata)), Env cdata userdata )
```

Users can provide the `tar` and `msg` type in `Scenes/Home/SceneBase.elm`.

Now let's consider implementing the example mentioned in [Introduction](../intro/model#example). You can either start a new project or use the hello world project.

Create a scene with three layers:

```bash
messenger scene Recursion
messenger layer Recursion A
messenger layer Recursion B
messenger layer Recursion C
```

Add them to the scene (`Scenes/Recursion/Model.elm`):

```elm
import Scenes.Recursion.A.Model as A
import Scenes.Recursion.B.Model as B
import Scenes.Recursion.C.Model as C
...
    , layers =
        [ A.layer NullLayerMsg envcd
        , B.layer NullLayerMsg envcd
        , C.layer NullLayerMsg envcd
        ]
```

To send integers between layers, we need a `IntMsg`. So edit `Scenes/Recursion/SceneBase.elm`:

```elm
type LayerMsg
    = IntMsg Int
    | NullLayerMsg
```

For layer A, edit `Scenes/Recursion/A/Model.elm`:

```elm
updaterec env msg data =
    case msg of
        IntMsg x ->
            if 0 <= x && x < 10 then
                ( data, [ Other ( "B", IntMsg (3 * x) ), Other ( "B", IntMsg (10 - 3 * x) ), Other ( "C", IntMsg x ) ], env )

            else
                ( data, [], env )

        _ ->
            ( data, [], env )
```

For layer B, edit `Scenes/Recursion/B/Model.elm`:

```elm
update env evt data =
    if env.globalData.sceneStartFrame == 10 then
        ( data, [ Other ( "A", IntMsg 2 ) ], ( env, False ) )

    else
        ( data, [], ( env, False ) )
```

and

```elm
updaterec env msg data =
    case msg of
        IntMsg x ->
            ( data, [ Other ( "A", IntMsg (x - 1) ) ], env )

        _ ->
            ( data, [], env )
```

Finally, for layer C, edit `Scenes/Recursion/C/Model.elm`:

```elm
updaterec env msg data =
    case msg of
        IntMsg x ->
            let
                _ =
                    Debug.log "C received" x
            in
            ( data, [], env )

        _ ->
            ( data, [], env )
```

Now `make` and see the result!
