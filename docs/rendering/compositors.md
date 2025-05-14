---
sidebar_position: 7
---

# Compositors

As seen before, an effect could be only applied to one renderable. What if I want to render something using multiple renderables?

The simplest example is `dstOverSrc`:

```elm
{-| Draw the dst renderable over the src renderable.
-}
dstOverSrc : Renderable -> Renderable -> Renderable
dstOverSrc src dst =
```

This is the default behavior when you list renderables in `REGL.group`. For example, `REGL.group [] [A, B, C]` is the same as `dstOverSrc (dstOverSrc A B) C`.

Commands like `dstOverSrc` are called compositors. They accept multiple renderables as inputs and output a renderable.

Compositors may also be nested.

## Built-in Compositors

### `dstOverSrc`

```elm
{-| Draw the dst renderable over the src renderable.
-}
dstOverSrc : Renderable -> Renderable -> Renderable
dstOverSrc src dst =
```

### `maskBySrc`

```elm
{-| Draw the dst renderable masked by the src renderable.
-}
maskBySrc : Renderable -> Renderable -> Renderable
maskBySrc src dst =
```

### `imgFade`

```elm
{-| Fading effect using a mask image.

Mask image is a gradient image, similar to [this](https://github.com/linsyking/elm-regl/blob/main/docs/asset/mask.jpg).

-}
imgFade : String -> Float -> Bool -> Renderable -> Renderable -> Renderable
imgFade mask t invert src dst =
```

### `linearFade`

```elm
{-| Linear fading effect.
-}
linearFade : Float -> Renderable -> Renderable -> Renderable
linearFade t src dst =
```

### `alpha`

```elm
{-| Set the alpha of a renderable. Only applicable to programs that support alpha field.
-}
alpha : Float -> Renderable -> Renderable
alpha a src =
```
