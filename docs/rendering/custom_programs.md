---
sidebar_position: 10
---

# Custom Programs

Users could write their own programs, effects and compositors.

Before writing the program, we must understand how the programs, effects and compositors work under the hood.

For all those programs, there exists two piece of code.

The first is the real implementation. This is written as a REGL program. This program defines how the renderer actually renders. It may take some parameters. Builtin programs are implemented in JS as it is more convenient and powerful. User-defined custom programs could be written in Elm, and must be provided to the render at the beginning because all the REGL programs are loaded before the game starts.
You can think of this as a system call implementation in the kernel. Take `triangle` program as an example. The `triangle` is implemented as follows:

```js
const triangle = () => [
    (x) => x,
    regl({
        frag: readFileSync('src/triangle/frag.glsl', 'utf8'),
        vert: readFileSync('src/triangle/vert.glsl', 'utf8'),
        attributes: {
            position: regl.prop('pos')
        },
        uniforms: {
            color: regl.prop('color')
        },
        count: 3
    })]
```

It defines the fragment and vertex shaders, and the parameters of the programs are `pos` and `color`.

The second is the call implementation. This is how the `triangle` function in Elm is implemented. Its definition is:

```elm
{-| Render a triangle with three vertices and color.
-}
triangle : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable
triangle ( x1, y1 ) ( x2, y2 ) ( x3, y3 ) color =
    genProg <|
        Encode.object
            [ ( "cmd", Encode.int 0 )
            , ( "prog", Encode.string "triangle" )
            , ( "args"
              , Encode.object
                    [ ( "pos", Encode.list Encode.float [ x1, y1, x2, y2, x3, y3 ] )
                    , ( "color", Encode.list Encode.float (toRgbaList color) )
                    ]
              )
            ]
```

As you can see, it generates a JSON object to represent the "call" to the real program. The parameters are encoded in this object. When the game is running, all those shim calls will be executed by an interpreter that actually calls the programs. You may think of those commands as
user-space library functions. They are only a wrapper of the real system call.

## Program Definition

Now let's first write a real REGL program in Elm.

In Messenger, all user-defined custom programs need to be registered by `allProgram` in `Lib/Resources.elm`.

## Extend Elm-regl with JS

