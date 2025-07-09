---
sidebar_position: 11
---

# Sprite Sheet

:::tip
The example is [here](https://github.com/linsyking/messenger-core/blob/main/test/src/Scenes/SpriteSheet/Model.elm).
:::

You could use `TextureRes` in a flexible way to construct spritesheet. Spritesheet is commonly used in pixel art games, RPG, etc.

To start with, you need to load a *spritesheet* image. The spritesheet image should contain all actions of the character. It looks like this:

![](/img/sheet.png)

## Loading Texture

There are multiple rows and columns in the spritesheet, so you need to get the positions and size of each action.

The way to specify part of the spritesheet image is through changing the `crop` option in texture loading options.

```elm
TextureRes "assets/img/sheet.png"
    (Just
        { mag = Just MagNearest
        , min = Nothing
        , crop = Just ( ( 0, 0 ), ( 32, 32 ) )
        }
    )
```

This is an example of `TextureRes` that loads a sprite from the spritesheet. The `crop` option defines the `(left, top)` pixel coordinate and `(width, height)` of the sprite. Since we are loading a pixel art image, we need to specify `mag` to be `MagNearest`.

However, this is for one sprite. There are many sprites in one spritesheet image, how to load all of them? Using the example spritesheet image above, there are 8 rows. Each row represents a series of actions and contains different number of sprites.

We note the sprite in X row and Y column `charXY`.

```elm
-- This is the number of sprites in each row
columnSize : List Int
columnSize =
    [ 13
    , 8
    , 10
    , 10
    , 10
    , 6
    , 4
    , 7
    ]


spriteSheet : ResourceDefs
spriteSheet =
    List.concat <|
        List.indexedMap
            (\row colsize ->
                List.map
                    (\col ->
                        ( "char" ++ String.fromInt row ++ String.fromInt col -- charXY
                        , TextureRes "assets/img/sheet.png"
                            (Just
                                { mag = Just MagNearest
                                , min = Nothing
                                , crop = Just ( ( 32 * col, 32 * row ), ( 32, 32 ) )
                                }
                            )
                        )
                    )
                <|
                    List.range 0 colsize
            )
            columnSize
```

Here two consecutive sprites have a distance of 32, so we can calculate each sprite's position statically.

## Displaying Motion

After adding the spritesheet resource, we need to show the sprites dynamically.

To render a single sprite, assuming it is at row 0 and column 4,

```elm
renderSprite id ( 0, 0 ) ( 100, 0 ) "char04"
```

The full view code that display the actions continuously:

```elm
view : RawSceneView UserData Data
view env data =
    let
        gd =
            env.globalData

        id =
            gd.internalData

        rate =
            100

        currentAct x =
            String.fromInt (modBy x (floor (gd.sceneStartTime / rate)))
    in
    group []
        [ renderSprite id ( 100, 300 ) ( 100, 0 ) ("char0" ++ currentAct 13)
        , renderSprite id ( 300, 300 ) ( 100, 0 ) ("char1" ++ currentAct 8)
        , renderSprite id ( 500, 300 ) ( 100, 0 ) ("char2" ++ currentAct 10)
        , renderSprite id ( 700, 300 ) ( 100, 0 ) ("char3" ++ currentAct 10)
        , renderSprite id ( 900, 300 ) ( 100, 0 ) ("char4" ++ currentAct 10)
        , renderSprite id ( 1100, 300 ) ( 100, 0 ) ("char5" ++ currentAct 6)
        , renderSprite id ( 1300, 300 ) ( 100, 0 ) ("char6" ++ currentAct 4)
        , renderSprite id ( 1500, 300 ) ( 100, 0 ) ("char7" ++ currentAct 7)
        ]
```

The result is like:

![](/img/spritesheet.png)