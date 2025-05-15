---
sidebar_position: 2
---

# Global Rendering Options

Elm-regl provides some initialization options:

- `fboNum`: The maximum number of Frame Buffer Objects allow to use in the game. Default is 5.
- `virtWidth`, `virtHeight`: virtual canvas size.
- `builtinPrograms`: the builtin programs enabled at the beginning of the game. See [Custom Programs](./custom_programs.md).

Every `REGL.group` uses a frame buffer, therefore, nested `group` will use more frame buffers. frame buffers are automatically garbage collected.

Those configuration could be changed in `MainConfig.elm`.

It also provides some options that could be changed during the game:

`timeInterval`: Controls the frame rate.

Users need to use an SOM call `ChangeFPS` to change the framerate during the game.
