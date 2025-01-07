---
sidebar_position: 1
---

# Events

There are two types of events in Messenger: _World Event_ and _User Event_. World event is the event Messenger can access while user event is the event users actually receive and it is a subset of the world event as some world event will not send to user.

```elm
type UserEvent
    = Tick Int
    | KeyDown Int
    | KeyUp Int
    | MouseDown Int ( Float, Float )
    | MouseUp Int ( Float, Float )
    | MouseWheel Int
    | Prompt String String
```

Every general model (layer, component) will use `UserEvent` when updating. Recall the `update` function:

```elm
update : env -> event -> data -> bdata -> ( ( data, bdata ), List (Msg tar msg sommsg), ( env, Bool ) )
```

The last `Bool` is a **block** indicator. Every general model is able to block event from sending to the next general components when updating.

:::note
The order of updating layers in a scene is opposite to the order of rendering layers in a scene. This mechanism is designed to enable layers to block messages from passing to the layers behind it. This is useful when you are handling mouse events. When a layer handles a mouse click event, you don't want other layers to also be triggered.
:::

Take an example, there are two layers in the scene in the order of `[A, B]` when initializing the scene. This means that A is rendered before B but A will be updated after B. Suppose the `update` of A emits `[A1, A2]` `SOMMsg`s and B emits `[B1, B2]` `SOMMsgs`, then the core will handle `SOMMsg` in the order of B1, B2, A1, A2.
