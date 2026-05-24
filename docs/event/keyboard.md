---
sidebar_position: 2
---

# Keyboard Event

## `KeyDown`

**Definition:** `KeyDown Int`

Triggered when a key is pressed.

## `KeyUp`

**Definition:** `KeyUp Int`

Triggered when a key is released.

Both events share the same parameter, which represents the [keyCode](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode) property of the keyboard event. The `keyCode` values of common keys can be found in `Messenger.Misc.KeyCode` (available in `messenger-extra`).

The following example shows how to handle keyboard events in a Layer or Component `update` function:

```elm
update runtime env evt data =
    case evt of
        KeyDown key ->
            if key == 13 then
                -- Enter key pressed: switch scene
                ( data, [ Parent <| SOMMsg <| SOMChangeScene Nothing "Menu" ], env )
            else
                ( data, [], env )
        KeyUp key ->
            ( data, [], env )
        _ ->
            ( data, [], env )
```

:::note
Users can use `getPressedKeys runtime` from `Messenger.Base` to get the current pressed keys.
:::
