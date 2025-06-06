---
sidebar_position: 5
---

# Virtual Scene Runner

:::warning
This feature is provided in the `messenger-extra` package.
:::

Users may want to run multiple scenes simultaneously, or use stacked scenes (which can remember your previous scene automatically).

Messenger provides several functions to allow users to run scenes in user code, like running "virtual machines".

The API is very simple, in `Messenge.Scene.VSR`:

```elm
{-| Virtual Scene Runner
-}
type alias VSR userdata scenemsg =
    { env : Env () userdata
    , scene : MAbstractScene userdata scenemsg
    }

{-| Update the VSR.
-}
updateVSR : VSR userdata scenemsg -> UserEvent -> ( VSR userdata scenemsg, List (SceneOutputMsg scenemsg userdata) )
updateVSR vsr evnt =

{-| View the VSR.
-}
viewVSR : VSR userdata scenemsg -> Renderable
```

So a `VSR` is just a `Env` with an abstract scene.

`updateVSR` and `viewVSR` are very straightforward. They are used to update and view the virtual scene.

But how to get the `scene`?

In global components, the common data `GCCommonData` is just `MAbstractScene` so we could get it easily.

For normal user code, if there is a scene defined as:

```elm
scene : SceneStorage UserData SceneMsg
```

Users could generate an abstract scene by initialize it with an initialization message and environment, since its type is:

```elm
type alias SceneStorage userdata scenemsg =
    Maybe scenemsg -> Env () userdata -> MAbstractScene userdata scenemsg
```
