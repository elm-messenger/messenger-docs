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

`AudioOption` is defined in `Messenger.Audio.Base.elm`:

```elm
type AudioOption
    = ALoop
    | AOnce
```

- `ALoop` means the audio will be played repeatedly.
- `AOnce` means the audio will be played only once.

### Example

Suppose we have two audio files `assets/bg.ogg` and `assets/se.ogg`.

First, we need to import them into our project, so edit `Lib/Resources.elm`:

```elm
allAudio : Dict.Dict String String
allAudio =
    Dict.fromList
        [ ( "bg", "assets/bg.ogg" )
        , ( "se", "assets/se.ogg" )
        ]
```

This is very similar to `allTexture`.

After that, we decide to use 0 as the background music channel and 1 as the sound effect channel.

Then, when we want to play the background music `bg`, emit:

```elm
SOMPlayAudio 0 "bg" ALoop
```

And when we want to play the sound effect `se`, emit:

```elm
SOMPlayAudio 1 "se" AOnce
```

:::tip
Users can use `newAudioChannel` to generate a unique channel ID.
:::

## `SOMStopAudio`

**Definition:** `SOMStopAudio Int`

This message is used to stop a channel. The parameter is the channel ID. If there are multiple audios playing on a channel, all of them will be stopped.

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

See @localstorage.

## `SOMGCLoad`, `SOMGCUnload`, `SOMCallGC`

**Definition:** `SOMLoadGC (GlobalComponentStorage userdata scenemsg)`

**Definition:** `SOMUnloadGC GCTarget`

**Definition:** `SOMCallGC (List ( GCTarget, GCMsg ))`

See @gc.

## Game Configurations

Users may want to change the settings in `MainConfig.elm` to match their demand. This section explains what each option in that configuration file means.

- `initScene`: The first scene users will see when starting the game
- `initSceneMsg`: The message to start the first scene
- `virtualSize`: The virtual drawing size. Users may use whatever they like but think carefully about the ratio (Support 4:3 or 16:9? screens)
- `debug`: A debug flag. If turned on, users can press `F1` to change to a scene quickly and press `F2` to change volume at any time in the game
- `background`: The background users see. Default is a transparent background
- `timeInterval`: The update strategy. See @tick
- `initGlobalData` and `saveGlobalData`: See @localstorage

## Messenger CLI Commands \<cli\>

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
