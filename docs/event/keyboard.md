---
sidebar_position: 2
---

# Keyboard Event

This includes both `KeyDown` and `KeyUp` events.

The parameter is `keyCode: Int`. `keyCode` is the [keyCode](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode) property of the keyboard event. `keyCode` of common keys can be found in `Messenger.Misc.KeyCode` (in `messenger-extra`).

:::note
Users can use `globalData.pressedKeys` to get the current pressed keys.
:::
