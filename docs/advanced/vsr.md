---
sidebar_position: 5
---

# Virtual Scene Runner

:::note
This feature is provided in the `messenger-core` package.
:::

Users may want to run multiple scenes simultaneously, or use stacked scenes (which can remember your previous scene automatically).

Messenger provides several functions to allow users to run scenes in user code, like running "virtual machines".

The API is very simple, in `Messenger.Scene.VSR`:

```elm
{-| Virtual Scene Runner
-}
type alias VSR userdata scenemsg =
    { env : Env () userdata
    , runtime : Runtime
    , scene : MAbstractScene userdata scenemsg
    }

{-| Update the VSR.
-}
updateVSR : VSR userdata scenemsg -> UserEvent -> ( VSR userdata scenemsg, List (SceneOutputMsg scenemsg userdata) )

{-| View the VSR.
-}
viewVSR : VSR userdata scenemsg -> Renderable
```

A `VSR` stores an `Env`, a `Runtime` (own timeline), and an abstract scene. `updateVSR` advances the virtual scene including its own time counters on `Tick`. `viewVSR` renders it.

But how to get the `scene`?

In global components, the common data `GCCommonData` is just `MAbstractScene` so we could get it easily.

For normal user code, if there is a scene defined as:

```elm
scene : SceneStorage UserData SceneMsg
```

Users could generate an abstract scene by initializing it with a message, runtime, and environment, since its type is:

```elm
type alias SceneStorage userdata scenemsg =
    Maybe scenemsg -> Runtime -> Env () userdata -> MAbstractScene userdata scenemsg
```
