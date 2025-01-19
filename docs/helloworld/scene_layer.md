---
sidebar_position: 3
---

# Scenes and Layers

## Scenes

## Layers

Users may need to handle `Parent` messages from components in a layer. Messenger provides a handy function `handleComponentMsgs` which is defined in `Messenger.Layer.Layer`, to help users handle those messages. Users need to provide a `MsgBase` handler, for example:

```elm
handleComponentMsg : Handler Data SceneCommonData UserData Target LayerMsg SceneMsg ComponentMsg
handleComponentMsg env compmsg data =
    case compmsg of
        SOMMsg som ->
            ( data, [ Parent <| SOMMsg som ], env )

        OtherMsg _ ->
            ( data, [], env )

        _ ->
            ( data, [], env )
```

Then users can combine it with `updateComponents` to define the `update` function in layers (provided in the Messenger template):

```elm
update : LayerUpdate SceneCommonData UserData LayerTarget LayerMsg SceneMsg Data
update env evt data =
    let
        ( comps1, msgs1, ( env1, block1 ) ) =
            updateComponents env evt data.components

        ( data1, msgs2, env2 ) =
            handleComponentMsgs env1 msgs1 { data | components = comps1 } [] handleComponentMsg
    in
    ( data1, msgs2, ( env2, block1 ) )
```
