---
sidebar_position: 7
---

# Texture

Before rendering textures in the game, users need to load the texture asset.

Users could add their texture resource in `src/Lib/Resources.elm`, like:

```elm
allTexture : ResourceDefs
allTexture =
    [ ( "ship", TextureRes "assets/enemy.png" Nothing )
    ]
```

The first element in the tuple is the name of the resource users want to use in the game.
The second element is the texture resource definition.

`TextureRes` takes an image path and texture options.

Usually users could use default texture options (`Nothing`), however, if users are creating pixel art, they should choose a different option (see below).

The texture option is defined by:

```elm
type alias TextureOptions =
    { mag : Maybe TextureMagOption
    , min : Maybe TextureMinOption
    , crop : Maybe ( ( Int, Int ), ( Int, Int ) )
    }

type TextureMagOption
    = MagNearest
    | MagLinear

type TextureMinOption
    = MinNearest
    | MinLinear
    | NearestMipmapNearest
    | LinearMipmapNearest
    | NearestMipmapLinear
    | LinearMipmapLinear
```

Use `Nothing` to use the default value.

:::tip
If users need to use pixel art, use `MagNearest` and `MinNearest`.
:::

`crop` means to crop the image. This is usually used for sprite sheet.

Two tuples are left-top coordinate and (width, height) of the cropped area. The unit is in pixel.

Elm REGL exposes several APIs for users to draw textures.

1. `texture`

Definition:

```elm
texture : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> String -> Renderable
texture ( x1, y1 ) ( x2, y2 ) ( x3, y3 ) ( x4, y4 ) name =
    ...
```

This is the most free but least used API. It allows users to sample the texture on a quad. Not recommended.

2. `rectTexture`

Definition:

```elm
rectTexture : ( Float, Float ) -> ( Float, Float ) -> String -> Renderable
rectTexture ( x, y ) ( w, h ) name =
    ...
```

This is commonly used.

Users could draw a rectangle with texture.

3. `centeredTexture`

Definition:

```elm
centeredTexture : ( Float, Float ) -> ( Float, Float ) -> Float -> String -> Renderable
centeredTexture ( x, y ) ( w, h ) angle name =
    ...
```

Also very common.

Users use the center coordinate, size and angle to draw a texture.


For each of three functions, elm REGL also provides its cropped version:

`textureCropped`, `rectTextureCropped` and `centeredTextureCropped`.

Take `rectTextureCropped` as example:

```elm
centeredTextureCropped : ( Float, Float ) -> ( Float, Float ) -> Float -> ( Float, Float ) -> ( Float, Float ) -> String -> Renderable
centeredTextureCropped ( x, y ) ( w, h ) angle ( cx, cy ) ( cw, ch ) name =
    ...
```

( cx, cy ) are the left-top texture coordinate and ( cw, ch ) are size of the cropped texture in texture coordinate.

Texture coordinates start at $(0,0)$ for the lower left corner of a texture image to $(1,1)$ for the upper right corner of a texture image.

To get the size of the texture, there is a function in `Messenger.Render.Texture`:

```elm
textureDim : InternalData -> String -> ( Int, Int )
```

Messenger also provides a handy function to render texture in `Messenger.Render.Texture`:

```elm
renderSprite : InternalData -> ( Float, Float ) -> ( Float, Float ) -> String -> Renderable
renderSprite gd position size name =
    ...
```

Users could only specify either height or width to let the function computes the other according to the image size by filling a zero.
For example, if users want to draw an image with width 100 pixel and keep the texture ratio, then they can write:

```elm
renderSprite gd position (100, 0) name
```

Moreover, users could directly use the texture size by using `(0,0)` as the size.
