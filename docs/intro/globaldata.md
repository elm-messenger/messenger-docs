---
sidebar_position: 4
---

# Global Data and Getters

`GlobalData` is the state Messenger carries across scenes. User code can write user-owned fields, while engine-owned fields are stored in opaque `internalData`.

```elm
type alias GlobalData userdata =
    { internalData : InternalData
    , extraHTML : Maybe (Html WorldEvent)
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

`internalData` is opaque. You can pass `env.globalData.internalData` to approved helpers, such as texture, coordinate, and audio helpers, but you should not inspect or update its fields.

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

- `getSceneStartTime : GlobalData userdata -> Float`
- `getGlobalStartTime : GlobalData userdata -> Float`
- `getGlobalStartFrame : GlobalData userdata -> Int`
- `getSceneStartFrame : GlobalData userdata -> Int`
- `getCurrentTimeStamp : GlobalData userdata -> Float`
- `getCurrentScene : GlobalData userdata -> String`

Input and browser state:

- `getWindowVisibility : GlobalData userdata -> Visibility`
- `getMousePos : GlobalData userdata -> ( Float, Float )`
- `getPressedMouseButtons : GlobalData userdata -> Set Int`
- `getPressedKeys : GlobalData userdata -> Set Int`

Canvas, volume, and resources:

- `getVolume : GlobalData userdata -> Float`
- `getVirtualSize : GlobalData userdata -> ( Float, Float )`
- `getRealSize : GlobalData userdata -> ( Float, Float )`
- `getViewPort : GlobalData userdata -> ( Float, Float )`
- `getCanvasOffset : GlobalData userdata -> ( Float, Float )`
- `getLoadingProgress : GlobalData userdata -> ( Int, Int )`
- `getFonts : GlobalData userdata -> Set String`
- `getPrograms : GlobalData userdata -> Set String`
- `getSprite : String -> GlobalData userdata -> Maybe REGL.Texture`
- `getAllSprites : GlobalData userdata -> Dict String REGL.Texture`
- `getConfigData : String -> GlobalData userdata -> Maybe String`

## Saving Global Data

`saveGlobalData` receives the full `GlobalData`.

```elm
saveGlobalData : GlobalData UserData -> String
saveGlobalData globalData =
    encodeUserData globalData.userData
```

If you need engine-owned values while saving, use getters instead of direct fields.
