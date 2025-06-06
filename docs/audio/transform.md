---
sidebar_position: 3
---

# Audio Transformation

Although users could use `AudioCommonOption` to specify playing rate and starting point, users may want to change some property of the playing stream when it is playing, such as fading effects.

Messenger allows users to transform an audio dynamically.

The definition of the SOM call is:

```elm
SOMTransformAudio AudioTarget (Audio -> Audio)
```

The first argument is the target, and the second argument if a *transformation function*.

Existing transformation functions are provided in elm-audio. See [its documentation](https://package.elm-lang.org/packages/MartinSStewart/elm-audio/latest/Audio#scaleVolume) for detailed explanation.

**Example 1.** Change the volume of a playing audio:

```elm
SOMTransformAudio (AudioName 0 "bg") (scaleVolume 0.5)
```

**Example 2.** Fading effects:

```elm
let
    ts =
        env.globalData.currentTimeStamp

    nts =
        Time.millisToPosix <| floor ts + 2000

    lts =
        Time.millisToPosix <| floor ts + 6000
in
    SOMTransformAudio (AudioName 0 "bg") (scaleVolumeAt [ ( Time.millisToPosix <| floor env.globalData.currentTimeStamp, 0 ), ( nts, 2 ), ( lts, 0 ) ])
```
