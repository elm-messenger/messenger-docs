---
sidebar_position: 8
---

# Effects

Effects are some visual effects applied after rendering a `REGL.group`. For example, we provide `gblur` effect which applies a [Gaussian Blur](https://en.wikipedia.org/wiki/Gaussian_blur) effect.

```elm Example Code
import REGL
import REGL.Effects as E

view = REGL.group [ ] [
    A,
    REGL.group [ E.gblur 10 ] [
        C, D
    ],
    B
]
```

In this example, A and B are two other renderables. A will be rendered first on the main "palette", C and D will be rendered using another new palette. The new palette will be applied with a `gblur` effect. C and D will be affected, A and B will not be affected.
Then the new palette will be rendered to the main palette. Finally B will be rendered.

Since C and D are rendered in a separate palette, you may want to set the background color to its parent, i.e.,

```elm Example Code
import REGL
import REGL.Programs as P
import REGL.Effects as E

view = REGL.group [] [
    P.clear Color.black,
    A,
    REGL.group [ E.gblur 10 ] [
        P.clear Color.black,
        C, D
    ],
    B
]
```

However, since the new palette cannot see the original palette, `gblur` cannot detect A. You may want the blurring effect to also capture the surrounding existing objects.
Using effect alone cannot solve this problem because it can be only applied to one renderable.

This feature could be achieved using [Compositors](./compositors.md) as compositors are designed for manipulate multiple renderables.

## Multiple Effects

Multiple effects could be used together. The order matters.

```elm
REGL.group [
    A,
    B,
    C
]
```

A effect will be applied before B, while B effect will be applied before C.

## Nested Effects

Nested effects are also supported.

```elm
REGL.group [ A ] [
    REGL.group [ B ] [
        C
    ]
]
```

Here C will be first applied with effect B, then applied with effect A.

## Built-in Effects

`Elm-REGL` provides several builtin effects.

### `blur`

Simple blur effects.

```elm
blur : Float -> List Effect
blur radius =
```

### `alphamult`

Multiply the alpha of the renderable with `a`.

```elm
alphamult : Float -> Effect
alphamult a =
```

:::warning
Using this effect to set the alpha of a single renderable may be slow. Use rendering commands with `alpha` to set alpha for texture rendering.
:::

### `fxaa`

Applies Fast approximate anti-aliasing (FXAA) to the renderable.

```elm
fxaa : Effect
fxaa =
```

### `gblur`

Applies Gaussian Blur.

```elm
gblur : Float -> List Effect
gblur sigma =
```

### `crt`

Applies the CRT effect. Must be applied outermost. `count` is the number of scan lines.

```elm
crt : Float -> Effect
crt count =
```

:::tip
You could also write your custom effects using shaders. See [Custom Programs](./custom_programs.md).
:::
