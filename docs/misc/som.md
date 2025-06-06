---
sidebar_position: 1
---

# SOM Calls

`SOMMsg`s are top-level APIs (like system calls in OS) that can directly interact with the core. Users can send `SOMMsg` in any general model.

## `SOMChangeScene`

**Definition:** `SOMChangeScene ( Maybe scenemsg ) String`

This message is used to change to another scene. Users need to provide the scene init data and the scene name.

## `SOMPlayAudio`

**Definition:** `SOMPlayAudio Int String AudioOption`

This message is used to play an audio. It has three parameters: channel ID, audio name, and audio option. The channel ID is where this audio will be played. There might be multiple audios playing on the same channel. Audio name is what users define in the keys of `allAudio`.

See [Audio Basics](../audio/basics.md) for more details.

## `SOMStopAudio`

**Definition:** `SOMStopAudio AudioTarget`

Stops a playing audio stream.

See [Audio Basics](../audio/basics.md) for more details.

## `SOMTransformAudio`

**Definition:** `SOMTransformAudio AudioTarget (Audio -> Audio)`

Transforms the audio on the fly.

See [Audio Transformation](../audio/transform.md) for more details.

## `SOMAlert`

**Definition:** `SOMAlert String`

This message is used to show an alert. The parameter is the content of the alert.

## `SOMPrompt`

**Definition:** `SOMPrompt String String`

This message is used to show a [prompt](https://developer.mozilla.org/en-US/docs/Web/API/Window/prompt). Users can use this to get text input from the user. The first parameter is the name of the prompt, and the second parameter is the title of the prompt.

When the user clicks the OK button, user code will receive a `Prompt String String` message. The first parameter is the name of the prompt, and the second parameter is the user’s input.

## `SOMSetVolume`

**Definition:** `SOMSetVolume Float`

This message is to change the volume. It should be a value in $[0, 1]$. Users could use a larger value but it will sound noisy.

## `SOMSaveGlobalData`

**Definition:** `SOMSaveGlobalData`

Save global data (including user data) to local storage.

See [Local Storage](../advanced/localstorage).

## `SOMGCLoad`, `SOMGCUnload`, `SOMCallGC`

**Definition:** `SOMLoadGC (GlobalComponentStorage userdata scenemsg)`

**Definition:** `SOMUnloadGC GCTarget`

**Definition:** `SOMCallGC (List ( GCTarget, GCMsg ))`

See [Global Component](../advanced/gc).

## `SOMChangeFPS`

**Definition:** `SOMChangeFPS REGL.TimeInterval`

Change FPS on the fly.

## `SOMLoadResource`

**Definition:** `SOMLoadResource String ResourceDef`

Dynamically load a resource at runtime. See [Dynamic Asset Loading](../advanced/assetload.md).

## Game Configurations

Users may want to change the settings in `MainConfig.elm` to match their demand. This section explains what each option in that configuration file means.

- `initScene`: The first scene users will see when starting the game
- `initSceneMsg`: The message to start the first scene
- `virtualSize`: The virtual drawing size. Users may use whatever they like but think carefully about the ratio (Support 4:3 or 16:9 screens)
- `debug`: A debug flag. If turned on, users can press `F1` to change to a scene quickly and press `F2` to change volume at any time in the game
- `background`: The background users see. Default is a transparent background
- `timeInterval`: The update strategy. See [Tick Event](../event/tick)
- `initGlobalData` and `saveGlobalData`: See [Local Storage](../advanced/localstorage)
- `fboNum`: Number of FBOs to enable at start.
- `enabledBuiltinPrograms`: Builtin REGL programs enabled at the beginning of the game.
