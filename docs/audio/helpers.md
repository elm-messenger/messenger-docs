---
sidebar_position: 4
---

# Audio Helpers

Most audio operations are done through SOM calls, but `Messenger.Audio.Audio` exposes two helpers for reading audio state.

## `audioDuration`

Use `audioDuration` to get the duration of a loaded audio resource.

```elm
import Messenger.Audio.Audio exposing (audioDuration)

audioDuration runtime "bg"
```

Its type is:

```elm
audioDuration : Runtime -> String -> Maybe Duration
```

The string is the audio resource name from `Resources.elm`. The result is `Nothing` if the audio has not been loaded or does not exist.

## `newAudioChannel`

Use `newAudioChannel` to find a fresh channel number.

```elm
import Messenger.Audio.Audio exposing (newAudioChannel)

channel =
    newAudioChannel runtime
```

Its type is:

```elm
newAudioChannel : Runtime -> Int
```

This is useful when a component wants to play audio without coordinating a fixed channel id with the rest of the game.
