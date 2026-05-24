---
sidebar_position: 3
---

# Mouse Event

## `MouseDown`

**Definition:** `MouseDown Int ( Float, Float )`

Triggered when a mouse button is pressed.

## `MouseUp`

**Definition:** `MouseUp Int ( Float, Float )`

Triggered when a mouse button is released.

The parameters of `MouseDown` and `MouseUp` represent `button` and `position`. 

- `button` indicates which mouse button was pressed. For example, 0 represents the left mouse button, and 2 represents the right mouse button. More values can be found in [MDN](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/button).

- `position` represents the virtual coordinate of the mouse.

:::tip
Current position of the mouse could also be got from `getMousePos runtime` from `Messenger.Base`.
:::

The following example shows how to detect a mouse click inside a rectangular area:

```elm
update runtime env evt data =
    case evt of
        MouseDown 0 pos ->
            if judgeMouseRect pos data.buttonPos data.buttonSize then
                ( data, [ Parent <| OtherMsg ButtonClicked ], ( env, True ) )
            else
                ( data, [], env )
        _ ->
            ( data, [], env )
```

## `MouseWheel`

**Definition:** `MouseWheel Int`

The parameter represents the vertical scroll amount `delta`, which corresponds to the [`deltaY`](https://developer.mozilla.org/en-US/docs/Web/API/WheelEvent/deltaY) property in [WheelEvent](https://developer.mozilla.org/en-US/docs/Web/API/WheelEvent).

:::note
Users can use `judgeMouseRect` in `Messenger.Coordinate.Coordinates` to check whether the mouse is in a rectangle.

The mouse coordinate is not transformed through the camera, so it is using the default UI camera starting from (0, 0) to (virtWidth, virtHeight).

To judge whether the mouse position is in a rectangle defined in a non-UI camera, users could use `judgeMouseRectWithCamera` to check.

To judge whether mouse is within a circle, use `judgeMouseCircle`. Users could also write there own `judge` functions.
:::

:::note
Users can use `getPressedMouseButtons runtime` from `Messenger.Base` to get the currently pressed mouse buttons.
:::
