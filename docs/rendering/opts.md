---
sidebar_position: 4
---

# Global Rendering Options

Elm-regl provides some initialization options:

- `virtWidth`, `virtHeight`: virtual canvas size.
- `builtinPrograms`: the builtin programs enabled at the beginning of the game. See [Custom Programs](./custom_programs.md).
- `fboNum`: The initial number of Frame Buffer Objects in the game. Default is 10. Normally you do not need to change this.

Every `REGL.group` uses a frame buffer, therefore, nested `group` will use more frame buffers. frame buffers are automatically garbage collected and created if not enough.

Those configuration could be changed in `MainConfig.elm`.

It also provides some options that could be changed during the game:

`timeInterval`: Controls the frame rate.

Users need to use an SOM call `ChangeFPS` to change the framerate during the game.
