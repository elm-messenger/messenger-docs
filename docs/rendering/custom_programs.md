---
sidebar_position: 10
---

# Custom Programs

:::warning
This is a very advanced usage of the renderer. Do not use this if you are not familiar with shaders (GLSL).
:::

Users could write their own programs, effects and compositors.

Before writing the program, we must understand how the programs, effects and compositors work under the hood.

For all those programs, there exists two piece of code.

The first is the real implementation. This is written as a REGL program. This program defines how the renderer actually renders. It may take some parameters. Builtin programs are implemented in JS as it is more convenient and powerful. User-defined custom programs could be written in Elm, and must be provided to the render at the beginning because all the REGL programs are loaded before the game starts.
You can think of this as a system call implementation in the kernel. Take `triangle` program as an example. The `triangle` is implemented as follows:

```js
const triangle = () => [
    (x) => x, // This is used to transform the parameters
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
    [ ( "_c", Encode.int 0 )
    , ( "_p", Encode.string "triangle" )
    , ( "pos", Encode.list Encode.float [ x1, y1, x2, y2, x3, y3 ] )
    , ( "color", Encode.list Encode.float (toRgbaList color) )
    ]
```

As you can see, it generates a JSON object to represent the "call" to the real program. The parameters are encoded in this object. When the game is running, all those shim calls will be executed by an interpreter that actually calls the programs. You may think of those commands as
user-space library functions. They are only a wrapper of the real system call.

## Program Definition

Now let's first write a real REGL program in Elm.

In Messenger, all user-defined custom programs need to be registered by `allProgram` in `Lib/Resources.elm`.

It is also possible to load a program later in the game by calling the `SOMLoadResource`.

First let's see the definition of a `Program`:

```elm

{-| A custom program that can be used with REGL.
-}
type alias REGLProgram =
    { frag : String
    , vert : String
    , attributes : Maybe (List ( String, ProgValue ))
    , uniforms : Maybe (List ( String, ProgValue ))
    , elements : Maybe ProgValue
    , primitive : Maybe ProgValue
    , count : Maybe ProgValue
    }
```

- `frag`: the fragment shader
- `vert`: the vertex shader
- `attributes`: the attributes used in shader
- `uniforms`: the uniforms used in shader

The program may have dynamic parameters (*e.g.*, positions). The parameters could be accessed using `ProgValue`.

```elm
{-| A value that can be either a static value or a dynamic value.
-}
type ProgValue
    = DynamicValue String
    | StaticValue Value
    | DynamicTextureValue String -- Dynamic texture value
```

`DynamicValue` is used for normal parameter passing, `DynamicTextureValue` is used to pass texture.

### Shaders

Elm-regl provides some pre-defined uniforms for writing shaders:

```glsl
uniform vec2 view;
uniform vec4 camera;
```

`view` is used to transform the view space into clip space shown on screen. It is defined as:

```
view = vec2(virtWidth/2, -virtHeight/2)
```

`virtWidth` and `virtHeight` are the virtual canvas size.

`camera` is the current camera, it is defined as:

```
camera = vec4(X, Y, Zoom, Rotation)
```

Let's use `rect`'s shaders as an example:

```glsl title="Vertex shader"
precision mediump float;

attribute vec2 position;
uniform vec4 posize;
uniform float angle;
uniform vec2 view;
uniform vec4 camera;

void main() {
    vec2 scaledVertex = (position - 0.5) * posize.zw;
    vec2 rotatedVertex = scaledVertex;

    if(angle != 0.) {
        // Rotate and scale the vertex
        mat2 rotation = mat2(cos(angle), -sin(angle), sin(angle), cos(angle));
        rotatedVertex = rotation * scaledVertex;
    }

    // Translate to the rectangle's position
    vec2 wpos = posize.xy + rotatedVertex;


    if (camera.w == 0.0){
        // No rotation
        vec2 pos = (wpos - camera.xy) * camera.z / view;
        gl_Position = vec4(pos, 0, 1);
    } else {
        mat2 rotation = mat2(cos(camera.w), -sin(camera.w), sin(camera.w), cos(camera.w));
        vec2 pos = (rotation * (wpos - camera.xy)) * camera.z / view;
        gl_Position = vec4(pos, 0, 1);
    }
}
```

You must use this code to transform `wpos` (world position) into screen position:

```glsl
if (camera.w == 0.0){
    vec2 pos = (wpos - camera.xy) * camera.z / view;
    gl_Position = vec4(pos, 0, 1);
} else {
    mat2 rotation = mat2(cos(camera.w), -sin(camera.w), sin(camera.w), cos(camera.w));
    vec2 pos = (rotation * (wpos - camera.xy)) * camera.z / view;
    gl_Position = vec4(pos, 0, 1);
}
```

```glsl title="Fragment shader"
precision mediump float;
uniform vec4 color;
void main() {
    gl_FragColor = color;
}
```

And it is initialized as:

```js
const rect = () => [
    (x) => x
    , regl({
        frag: frags["rect"],
        vert: verts["rect"],
        attributes: {
            position: [
                0, 1,
                1, 1,
                1, 0,
                0, 0,
            ]
        },
        uniforms: {
            posize: regl.prop('posize'),
            angle: regl.prop('angle'),
            color: regl.prop('color')
        },
        elements: [
            0, 1, 2,
            0, 2, 3
        ],
        count: 6
    })]
```

:::tip
See the following examples:
- [Mask](https://github.com/elm-messenger/elm-regl/blob/main/test/src/Mask.elm)
- [Particle](https://github.com/elm-messenger/elm-regl/blob/main/test/src/Particle.elm)
:::

## Call Definition

Besides the program definition, you also need a call function. It is defined by:

```elm
type alias ProgramCall =
    List ( String, Value )
```

So it is basically a list of string and value pair.

An example:

```elm
triangle : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable
triangle ( x1, y1 ) ( x2, y2 ) ( x3, y3 ) color =
    [ ( "_c", Encode.int 0 )
    , ( "_p", Encode.string "triangle" )
    , ( "pos", Encode.list Encode.float [ x1, y1, x2, y2, x3, y3 ] )
    , ( "color", Encode.list Encode.float (toRgbaList color) )
    ]
```

`_c` and `_p` are necessary fields to call your program. `_c`'s value pair must be `0` and `_p`'s should be a string of your program name.

## Extend Elm-regl with JS

Although Elm-regl wraps custom program APIs in Elm, users may want to use more advanced programs. We provide an option to write programs in JS.

In `public/elm-messenger.js`, you could see this:

```html
ElmREGL.init(canvas, app, []);
```

You could import your custom program written in JS after the initialization using this:


```js
ElmREGL.loadGLProgram(name, f)
```

`name` is the program name like `blur`, `f` is a function that used to initialize the program. `f` will be fed with a `regl` variable which is the renderer.

An example:

```js
ElmREGL.loadGLProgram("myprog", (regl) => [
    (x) => x,
    regl({
        frag: "blabla",
        vert: "blabla,
        attributes: {
            position: regl.prop('pos')
        },
        uniforms: {
            color: regl.prop('color')
        },
        count: 3
    })])
```

See how built-in programs are written in JS in the [source code](https://github.com/elm-messenger/elm-regl-js/blob/main/src/app.js).
