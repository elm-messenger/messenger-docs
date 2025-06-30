---
sidebar_position: 2
---

# Example: Space Shooter

The source code is available at [messenger core test](https://github.com/elm-messenger/messenger-core/tree/main/test/src/SceneProtos/Spaceshooter).

Now let's design a space shooter game that has several levels but with the same type of player and enemy with different parameters.

We will only go through the most important parts; other details are in the code.

## Sceneproto and Layer Initialization

First, run the following commands to initialize the project:

```bash
messenger init spaceshooter
cd spaceshooter
messenger scene Game -p
messenger component Game Bullet -i -p
messenger component Game Enemy -i -p
messenger component Game Ship -i -p
messenger layer Game Main -c -i -p
messenger level Game Level1
```

Next, add layer `Main` to `Game`:

```elm
...
    , layers =
        [ Main.layer NullLayerMsg envcd
        ]
```

Let's first design the `InitData` of our scene and layer. Since we want each level to have different enemies and parameters, we may want to directly use a list of components as the `InitData`.

Add `InitData` in `SceneProtos/Game/Main/Init.elm`:

```elm
type alias InitData cdata scenemsg =
    { components : List (AbstractComponent cdata UserData ComponentTarget ComponentMsg BaseData scenemsg)
    }
```

:::note
We cannot use `SceneCommonData` or `SceneMsg`. The reason is cycle import explained in [init](../helloworld/init). Please read that diagram carefully.
:::

Then we add this `InitData` to the `LayerMsg` of `Game` (`SceneProtos/Game/SceneBase.elm`):

```elm
import SceneProtos.Game.Main.Init as MainInit
...
type LayerMsg scenemsg
    = MainInitData (MainInit.InitData SceneCommonData scenemsg)
    | NullLayerMsg
```

For the same reason, we cannot use `SceneMsg`.

Define the `InitData` of the scene. Here it should be almost the same as the layer `InitData` (`SceneProtos/Game/Init.elm`).

```elm
type alias InitData scenemsg =
    { objects : List (LevelComponentStorage SceneCommonData UserData ComponentTarget ComponentMsg BaseData scenemsg)
    }
```

Here `LevelComponentStorage` is a type sugar to store components that have initialized `Msg` but not `Env`. See the difference from its definition:

```elm
type alias ComponentStorage cdata userdata tar msg bdata scenemsg =
    msg -> LevelComponentStorage cdata userdata tar msg bdata scenemsg

type alias LevelComponentStorage cdata userdata tar msg bdata scenemsg =
    Env cdata userdata -> AbstractComponent cdata userdata tar msg bdata scenemsg
```

Finally, let's modify `Lib/Base.elm` to add the scene `InitData`:

```elm
import SceneProtos.Game.Init as GameInit
...
type SceneMsg
    = GameInitData (GameInit.InitData SceneMsg)
    | NullSceneMsg
```

It is here to use `SceneMsg`.

## Component Base

After writing `Init` and `Base` for layers and scenes, let's deal with components.

We first define the `InitData` for all the components. For the enemy, edit `SceneProtos/Game/Components/Enemy/Init.elm`:

```elm
type alias InitData =
    { id : Int
    , velocity : Float
    , position : ( Float, Float )
    , sinF : Float
    , sinA : Float
    , bulletInterval : Int
    }
```

The other two components are similar.

For the bullet, the `CreateInitData` is used to create a new bullet during the game running. The `id` is determined by the layer so it's not in `CreateInitData`.

Finally, we write `SceneProtos.Game.Components.ComponentBase`:

```elm
import SceneProtos.Game.Components.Bullet.Init as Bullet
import SceneProtos.Game.Components.Enemy.Init as Enemy
import SceneProtos.Game.Components.Ship.Init as Ship

type ComponentMsg
    = NewBulletMsg Bullet.CreateInitData
    | CollisionMsg String
    | GameOverMsg
    | BulletInitMsg Bullet.InitData
    | EnemyInitMsg Enemy.InitData
    | ShipInitMsg Ship.InitData
    | NullComponentMsg
    -- You may add more here

type ComponentTarget
    = Type String
    | Id Int

type alias BaseData =
    { id : Int
    , ty : String
    , position : ( Float, Float )
    , velocity : Float
    , collisionBox : ( Float, Float )
    , alive : Bool
    }
```

Here we define the message type, target, and the base data type.

## Component Models

Now let's write model files for components. Here we only write the bullet model. Others are similar.

First, define the data type and `init` function:

```elm
type alias Data =
    { color : Color
    }

init env initMsg =
    case initMsg of
        BulletInitMsg msg ->
            ( { color = msg.color }
            , { id = msg.id
              , position = msg.position
              , velocity = msg.velocity
              , alive = True
              , collisionBox = ( 20, 10 )
              , ty = "Bullet"
              }
            )

        _ ->
            ( { color = Color.black }, emptyBaseData )
```

We need to initialize the base data in `init` function.

Then, for `update` function, we want the moving bullet to move a small step on every `Tick`.

```elm
update env evnt data basedata =
    case evnt of
        Tick dt ->
            let
                newBullet =
                    { basedata | position = ( Tuple.first basedata.position + basedata.velocity * toFloat dt, Tuple.second basedata.position ) }
            in
            ( ( data, newBullet ), [], ( env, False ) )

        _ ->
            ( ( data, basedata ), [], ( env, False ) )
```

For `updaterec`, when a bullet hits a bullet, they should all disappear.

```elm
updaterec env msg data basedata =
    case msg of
        CollisionMsg "Bullet" ->
            ( ( data, { basedata | alive = False } ), [], env )

        _ ->
            ( ( data, basedata ), [], env )
```

For `view`, we render a round rectangle with a given color. The `matcher` can match both `Id` and `Type`.

```elm
import REGL.BuiltinPrograms as P
view env data basedata =
    let
        gd =
            env.globalData.internalData
    in
    ( P.rect basedata.position (20, 10) data.color, 0 )

matcher data basedata tar =
    tar == Type basedata.ty || tar == Id basedata.id
```

## Layer Model

The layer needs to manage all the components and handle the collisions.

Therefore, we first write a collision handler to deal with collisions. `updateCollision` will send `CollisionMsg` to components that have collisions. See the source code for how to implement the collision updater.

```elm
updateCollision : Env SceneCommonData UserData -> List GameComponent -> ( List GameComponent, List (MMsgBase ComponentMsg SceneMsg UserData), Env SceneCommonData UserData )
```

Three helper functions are also used:

- `removeDead`: remove dead components
- `removeOutOfBound`: remove components that are out of bound
- `genUID`: generate a new unique ID from the list of components

In `SceneProtos.Game.Main.Model`, most parts are easy to write. The `handleComponentMsg` that handles messages from the component might be tricky. It needs to create a new component if it receives `NewBulletMsg`.

```elm
import SceneProtos.Game.Components.Bullet.Model as Bullet
...
handleComponentMsg env compmsg data =
    case compmsg of
        SOMMsg som ->
            ( data, [ Parent <| SOMMsg som ], env )

        OtherMsg msg ->
            case msg of
                NewBulletMsg initData ->
                    let
                        objs =
                            data.components

                        newBulletInitMsg =
                            BulletInitMsg
                                { id = genUID objs
                                , position = initData.position
                                , velocity = initData.velocity
                                , color = initData.color
                                }

                        newBullet =
                            Bullet.component newBulletInitMsg env

                        newObjs =
                            newBullet :: objs
                    in
                    ( { data | components = newObjs }, [], env )

                _ ->
                    ( data, [], env )
```

## Sceneproto Model

We need to update the sceneproto model to initialize the components. See the source code for details.

## Level Model

Lastly, we can implement a level:

```elm
import SceneProtos.Game.Components.Enemy.Model as Enemy
import SceneProtos.Game.Components.Enemy.Init as EnemyInit
import SceneProtos.Game.Components.Ship.Model as Ship
import SceneProtos.Game.Components.Ship.Init as ShipInit
import SceneProtos.Game.Init exposing (InitData)
import SceneProtos.Game.Model exposing (genScene)
...

init : RawSceneProtoLevelInit UserData SceneMsg (InitData SceneMsg)
init env msg =
    Just (initData env msg)

initData : Env () UserData -> Maybe SceneMsg -> InitData SceneMsg
initData env msg =
    { objects =
        [ Ship.component (ShipInitMsg <| ShipInit.InitData 0 ( 100, 500 ) 15)
        , Enemy.component (EnemyInitMsg <| EnemyInit.InitData 1 ( -1 / 15 ) ( 1920, 1000 ) 50 10 25)
        ]
    }
```

:::note
`env` is not used to initialize the components. This is because component initialization needs common data and that should happen during the initialization of the sceneproto.
:::
