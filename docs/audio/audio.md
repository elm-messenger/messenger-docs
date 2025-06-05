---
sidebar_position: 1
---

# Audio System

Messenger provides a *stateful* audio player, which is based on a Javascript audio player and its [elm wrapper](https://package.elm-lang.org/packages/MartinSStewart/elm-audio/latest/).

Like text and texture rendering, audio also needs resource files in `Resources.elm`. Before playing any audio, please define them in `allAudio`:

```elm
{-| All audio assets.

The format is similar to `allTexture`.

Example:

        [ ( "test", AudioRes "assets/test.ogg" )
        ]

-}
allAudio : ResourceDefs
allAudio =
```

Then, you are ready to play audio in Messenger. All the audio operations are implemented as SOM calls.

## Audio Abstraction

Messenger provides an infinite number of audio *channels* that could play an audio file.

One audio channel could play multiple audio files at a time, and different audio channels could also play different audio concurrently.

When an audio file is playing, it becomes a *playing stream*. There may be multiple playing stream for a single file.

The playing stream could be found by the channel ID and the audio name.

:::tip
See [demo](https://github.com/elm-messenger/messenger-core/blob/main/test/src/Scenes/Audio/Model.elm).
:::
