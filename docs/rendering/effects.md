---
sidebar_position: 8
---

# Effects

Effects are some visual effects applied after rendering a `REGL.group`. For example, we provide `gblur` effect which applies a [Gaussian Blur](https://en.wikipedia.org/wiki/Gaussian_blur) effect.

```elm
import REGL
import REGL.Effects as E

view = REGL.group [ ] [
    A,
    REGL.group (E.gblur 3) [
        C, D
    ],
    B
]
```

In this example, A and B are two other renderables. A will be rendered first on the main "palette", C and D will be rendered using another new palette. The new palette will be applied with a `gblur` effect. C and D will be affected, A and B will not be affected.
Then the new palette will be rendered to the main palette. Finally B will be rendered.

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

### `colormult`

Multiply the color of the renderable with `rgba`.

```elm
alphamult : Float -> Float -> Float -> Float -> Effect
alphamult r g b a =
```

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

### `pixilation`

Applies the pixilation effect. `ps` is the number of pixels that will be considered as a same pixel on the screen.

```elm
{-| Apply pixilation effect to a renderable.
-}
pixilation : Float -> Effect
pixilation ps =
```

:::tip
You could also write your custom effects using shaders. See [Custom Programs](./custom_programs.md).
:::
