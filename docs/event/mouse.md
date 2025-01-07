---
sidebar_position: 3
---

# Mouse Event

This includes both `MouseDown` and `MouseUp` events.

The parameters are `button: Int`, `position: (Float, Float)`. The button has many values. 0 is the left mouse button and 2 is the right mouse button. More values can be found in [MDN](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/button).

`position` is the virtual coordinate of the mouse.

Users can use `judgeMouseRect` in `Messenger.Coordinate.Coordinates` to check whether the mouse is in a rectangle.

:::note
Users can use `globalData.pressedMouseButtons` to get the current pressed mouses.
:::
