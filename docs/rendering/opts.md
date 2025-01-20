---
sidebar_position: 2
---

# Global Rendering Options

Elm-regl provides some initialization options:

- `fboNum`: The maximum number of Frame Buffer Objects allow to use in the game. Default is 5.
- `virtWidth`, `virtHeight`: virtual canvas size.
- `builtinPrograms`: the builtin programs enabled at the beginning of the game.

Those configuration could be changed in `MainConfig.elm`.

:::warning
TODO: explain this further.
:::

It also provides some options that could be changed during the game:

`timeInterval`: Controls the frame rate.

Users need to use an SOM call `ChangeFPS` to change the framerate during the game.