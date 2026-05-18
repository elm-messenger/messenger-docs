---
sidebar_position: 4
---

# Global Data and Getters

`GlobalData` is the user-owned state Messenger carries across scenes. Engine-owned runtime state is kept separately in a read-only `Runtime` value that user code receives beside `Env`.

```elm
type alias GlobalData userdata =
    { extraHTML : Maybe (Html WorldEvent)
    , canvasAttributes : List (Html.Attribute WorldEvent)
    , userData : userdata
    , camera : Camera
    }
```

## Writable Fields

- `userData`: user-owned data, usually the data you save to local storage.
- `extraHTML`: optional extra HTML rendered together with the canvas.
- `canvasAttributes`: custom attributes attached to the canvas.
- `camera`: the global camera used for rendering the scene.

`Runtime` is opaque and read-only. User code can pass the runtime value to approved helpers, such as texture, coordinate, and audio helpers, but cannot inspect or replace its fields. User update functions return only `Env`, not `Runtime`.

## Initial Global Data

`MainConfig.initGlobalData` returns `GlobalDataInit`, not a full `GlobalData`.

```elm
type alias GlobalDataInit userdata =
    { camera : Camera
    , volume : Float
    , extraHTML : Maybe (Html WorldEvent)
    , canvasAttributes : List (Html.Attribute WorldEvent)
    , userData : userdata
    }
```

Messenger initializes runtime fields such as time, current scene, pressed keys, mouse position, loaded resources, and audio state.

## Getter Groups

Import getters from `Messenger.Base`.

```elm
import Messenger.Base exposing (getSceneStartTime, getMousePos, getPressedKeys)
```

Time and scene:

- `getSceneStartTime : Runtime -> Float`
- `getGlobalStartTime : Runtime -> Float`
- `getGlobalStartFrame : Runtime -> Int`
- `getSceneStartFrame : Runtime -> Int`
- `getCurrentTimeStamp : Runtime -> Float`
- `getCurrentScene : Runtime -> String`

Input and browser state:

- `getWindowVisibility : Runtime -> Visibility`
- `getMousePos : Runtime -> ( Float, Float )`
- `getPressedMouseButtons : Runtime -> Set Int`
- `getPressedKeys : Runtime -> Set Int`

Canvas, volume, and resources:

- `getVolume : Runtime -> Float`
- `getVirtualSize : Runtime -> ( Float, Float )`
- `getRealSize : Runtime -> ( Float, Float )`
- `getViewPort : Runtime -> ( Float, Float )`
- `getCanvasOffset : Runtime -> ( Float, Float )`
- `getLoadingProgress : Runtime -> ( Int, Int )`
- `getFonts : Runtime -> Set String`
- `getPrograms : Runtime -> Set String`
- `getSprite : String -> Runtime -> Maybe REGL.Texture`
- `getAllSprites : Runtime -> Dict String REGL.Texture`
- `getConfigData : String -> Runtime -> Maybe String`

## Saving Global Data

`saveGlobalData` receives the read-only `Runtime` and the writable `GlobalData`.

```elm
saveGlobalData : Runtime -> GlobalData UserData -> String
saveGlobalData runtime globalData =
    encodeUserData globalData.userData
```

If you need engine-owned values while saving, read them from `runtime` with getters.
