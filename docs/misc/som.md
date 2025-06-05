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

Dynamically load a resource at runtime.

## Game Configurations

Users may want to change the settings in `MainConfig.elm` to match their demand. This section explains what each option in that configuration file means.

- `initScene`: The first scene users will see when starting the game
- `initSceneMsg`: The message to start the first scene
- `virtualSize`: The virtual drawing size. Users may use whatever they like but think carefully about the ratio (Support 4:3 or 16:9 screens)
- `debug`: A debug flag. If turned on, users can press `F1` to change to a scene quickly and press `F2` to change volume at any time in the game
- `background`: The background users see. Default is a transparent background
- `timeInterval`: The update strategy. See [Tick Event](../event/tick)
- `initGlobalData` and `saveGlobalData`: See [Local Storage](../advanced/localstorage)
- `fboNum`: Number of FBOs to enable.
- `enabledBuiltinPrograms`: Builtin REGL programs enabled at the beginning of the game.

## Messenger CLI Commands

You can also use `messenger <command> --help` to view help.

## Scene

Create a scene.

Usage: `messenger scene [OPTIONS] NAME`

Arguments:

- `name`: The name of the scene
- `--raw`: Use raw scene without layers
- `--proto`, `-p`: Create a sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Init

Initialize a Messenger project.

Usage: `messenger init [OPTIONS] NAME`

Arguments:

- `name`: The name of the project
- `--template-repo`, `-t`: Use a customized repository for cloning templates.
- `--template-tag`, `-b`: The tag or branch of the repository to clone.
- `--use-cdn`: Use jsdelivr CDN for elm-regl JS file.
- `--min`: Use minimal regl JS that has no builtin font.

## Layer

Create a layer.

Usage: `messenger layer [OPTIONS] NAME LAYER`

Arguments:

- `name`: The name of the scene
- `layer`: The name of the layer
- `--with-component`, `-c`: Use components in this layer
- `--cdir`, `-cd`: Directory of components in the scene
- `--proto`, `-p`: Create layer in sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Level

Create a level.

Usage: `messenger level [OPTIONS] SCENEPROTO NAME`

Arguments:

- `sceneproto`: The name of the sceneproto
- `name`: The name of the level

## Component

Create a component.

Usage: `messenger component [OPTIONS] SCENE NAME`

Arguments:

- `scene`: The name of the scene
- `name`: The name of the component
- `--cdir`, `-cd`: Directory to store components
- `--proto`, `-p`: Create component in sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Global Component

Create a global component.

Usage: `messenger gc [OPTIONS] NAME`

Arguments:

- `name`: The name of the component

## Update

Update `messenger.json` based on existing files.

## Remove

Remove a scene or a sceneproto.

Usage: `messenger remove [OPTIONS] TYPE NAME`

Arguments:

- `type`: Either `"scene"` or `"sceneproto"`
- `name`: The name of the scene or sceneproto
- `--rm`: Also remove the modules
- `--rml`: Remove all levels in the sceneproto
- `--help`: Show this message and exit
