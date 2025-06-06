---
sidebar_position: 2
---

# Coordinate and Camera System

By default Messenger integrates Elm-regl's camera and virtual coordinate system.

The default virtual canvas size is `1920x1080`.

The following is an illustration of the coordinate system:

![](/img/coordinate.png)

The coordinate increases on the east and south direction.

Note that this is only virtual coordinates used in Messenger, and the actual canvas size may vary based on screen size.

## Camera System

Messenger provides with a Global Camera that could be set in `globalData.camera` and many Local Cameras that could override the global camera's rule.

A camera setting is defined as:

```elm

{-| Camera settings.
-}
type alias Camera =
    { x : Float
    , y : Float
    , zoom : Float
    , rotation : Float
    }
```

The `x` and `y` are the center coordinates of the camera. The camera will capture a canvas in the virtual coordinate system of size specified in virtual canvas size.

By default, `x` and `y` are set to half of the virtual canvas size, which in our case is `960` and `540`.
`zoom` is 1 and `rotation` is 0 by default. The default camera is also called UI camera as it is mainly used for UI drawing.

A great feature of Messenger's camera system is that the global camera setting could be **override** by using `groupWithCamera`.

```elm
groupWithCamera : Camera -> List Effect -> List Renderable -> Renderable
```

You may even override the camera inside a `groupWithCamera`:

```elm
groupWithCamera camera1 [
    groupWithCamera camera2 [
        ...
    ]
]
```

You could use the default camera (UI camera) by calling `defaultCamera` from `Messenger.Coordinate.Camera`.

## Manual Coordinate System Transformation

In some cases, users may want to manually transform a coordinate in world space to view space, or vice versa. Messenger provides two handy functions in `Messenger.Coordinate.Camera`:

```elm
{-| Tranform a position from the world coordinate system to the view (camera) coordinate system.
-}
worldToView : Camera -> ( Float, Float ) -> ( Float, Float )
worldToView camera ( x, y ) =

{-| Transform a position from the view coordinate system to the world coordinate system.

This is the inverse of `worldToView`.

-}
viewToWorld : Camera -> ( Float, Float ) -> ( Float, Float )
viewToWorld camera ( x, y ) =
```
