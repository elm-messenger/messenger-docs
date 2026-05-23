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
messenger layer Game Front --proto -p
messenger level Game Level1
```

Next, add layers `Main` and `Front` to `Game`:

```elm
...
    , layers =
        [ Main.layer (MainInitData { components = comps }) runtime envcd
        , Front.layer (FrontInitData levelName) runtime envcd
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
    | FrontInitData String
    | NullLayerMsg
```

For the same reason, we cannot use `SceneMsg`.

Define the `InitData` of the scene (`SceneProtos/Game/Init.elm`):

```elm
type alias InitData scenemsg =
    { objects : List (LevelComponentStorage SceneCommonData UserData ComponentTarget ComponentMsg BaseData scenemsg)
    , level : String
    }
```

Here `LevelComponentStorage` is a type sugar to store components that have initialized `Msg` but not `Runtime` or `Env`:

```elm
type alias ComponentStorage cdata userdata tar msg bdata scenemsg =
    msg -> LevelComponentStorage cdata userdata tar msg bdata scenemsg

type alias LevelComponentStorage cdata userdata tar msg bdata scenemsg =
    Runtime -> Env cdata userdata -> AbstractComponent cdata userdata tar msg bdata scenemsg
```

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
    , bulletInterval : Float
    }
```

For the bullet (`SceneProtos/Game/Components/Bullet/Init.elm`):

```elm
type alias InitData =
    { id : Int
    , velocity : Float
    , position : ( Float, Float )
    , color : Color
    }

type alias CreateInitData =
    { velocity : Float
    , position : ( Float, Float )
    , color : Color
    }
```

The `CreateInitData` is used to create a new bullet during the game running. The `id` is determined by the layer so it's not in `CreateInitData`.

For the ship (`SceneProtos/Game/Components/Ship/Init.elm`):

```elm
type alias InitData =
    { id : Int
    , position : ( Float, Float )
    , bulletInterval : Float
    }
```

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

emptyBaseData : BaseData
emptyBaseData =
    { id = 0
    , position = ( 0, 0 )
    , velocity = 0
    , collisionBox = ( 0, 0 )
    , alive = True
    , ty = ""
    }
```

Here we define the message type, target, the base data type, and a default `emptyBaseData`.

## Component Models

Now let's write model files for components. Here we write the bullet model. Others are similar.

First, define the data type and `init` function:

```elm
type alias Data =
    { color : Color
    }

init : ComponentInit SceneCommonData UserData ComponentMsg Data BaseData
init _ _ initMsg =
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

Note that `init` takes `runtime` and `env` as its first two parameters (ignored with `_` here). We initialize the base data in `init`.

Then, for `update`, we want the bullet to move a small step on every `Tick`:

```elm
update : ComponentUpdate SceneCommonData Data UserData SceneMsg ComponentTarget ComponentMsg BaseData
update _ env evnt data basedata =
    case evnt of
        Tick dt ->
            let
                newBullet =
                    { basedata | position = ( Tuple.first basedata.position + basedata.velocity * dt, Tuple.second basedata.position ) }
            in
            ( ( data, newBullet ), [], ( env, False ) )

        _ ->
            ( ( data, basedata ), [], ( env, False ) )
```

For `updaterec`, when a bullet hits another bullet, they should both disappear:

```elm
updaterec : ComponentUpdateRec SceneCommonData Data UserData SceneMsg ComponentTarget ComponentMsg BaseData
updaterec _ env msg data basedata =
    case msg of
        CollisionMsg "Bullet" ->
            ( ( data, { basedata | alive = False } ), [], env )

        _ ->
            ( ( data, basedata ), [], env )
```

For `view`, we render a rounded rectangle with the given color. The `matcher` can match both `Id` and `Type`.

```elm
import REGL.BuiltinPrograms as P
view : ComponentView SceneCommonData UserData Data BaseData
view _ _ data basedata =
    ( P.roundedRect basedata.position ( 20, 10 ) 5 data.color, 0 )

matcher : ComponentMatcher Data BaseData ComponentTarget
matcher _ basedata tar =
    tar == Type basedata.ty || tar == Id basedata.id
```

Other components follow the same pattern. The enemy model additionally uses `getSceneStartTime runtime` in its `Tick` handler for sinusoidal movement and emits `NewBulletMsg` periodically. The ship model uses `getPressedKeys runtime` in `KeyUp` to detect whether arrow keys are still held.

## Layer Model

The layer needs to manage all the components and handle the collisions.

Therefore, we first write a collision handler to deal with collisions. `judgeCollision` will generate `(target, CollisionMsg)` pairs for components that have collisions. See the source code for how to implement the collision logic.

```elm
judgeCollision : List GameComponent -> List ( ComponentTarget, ComponentMsg )
```

Three helper functions are also used:

- `removeDead`: remove dead components (filtering by `baseData.alive`)
- `removeOutOfBound`: remove components that are out of bounds
- `genUID`: generate a new unique ID from the list of components

In `SceneProtos.Game.Main.Model`, the `handleComponentMsg` that handles messages from the components needs to create a new component if it receives `NewBulletMsg`.

```elm
import SceneProtos.Game.Components.Bullet.Model as Bullet
...
handleComponentMsg : Handler Data SceneCommonData UserData LayerTarget (LayerMsg SceneMsg) SceneMsg ComponentMsg
handleComponentMsg runtime env compmsg data =
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
                            Bullet.component newBulletInitMsg runtime env

                        newObjs =
                            newBullet :: objs
                    in
                    ( { data | components = newObjs }, [], env )

                GameOverMsg ->
                    let
                        cd =
                            env.commonData
                    in
                    ( data, [], { env | commonData = { cd | gameOver = True } } )

                _ ->
                    ( data, [], env )
```

Note the `runtime` parameter in `handleComponentMsg` and its use in `Bullet.component newBulletInitMsg runtime env`.

## Sceneproto Model

We need to update the sceneproto model to initialize the components. In `SceneProtos/Game/Model.elm`:

```elm
commonDataInit : Runtime -> Env () UserData -> Maybe (InitData SceneMsg) -> SceneCommonData
commonDataInit _ _ _ =
    { score = 0
    , gameOver = False
    }

init : LayeredSceneProtoInit SceneCommonData UserData LayerTarget (LayerMsg SceneMsg) SceneMsg (InitData SceneMsg)
init runtime env data =
    let
        cd = commonDataInit runtime env data
        envcd = addCommonData cd env
        comps = List.map (\x -> x runtime envcd) (Maybe.withDefault [] (Maybe.map .objects data))
        levelName = Maybe.withDefault "" (Maybe.map .level data)
    in
    { renderSettings = []
    , commonData = cd
    , layers =
        [ Main.layer (MainInitData { components = comps }) runtime envcd
        , Front.layer (FrontInitData levelName) runtime envcd
        ]
    }
```

The components from the level's `InitData` are initialized by applying `runtime envcd` to each `LevelComponentStorage`, which completes the component creation.

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

initData : Env () UserData -> Maybe SceneMsg -> InitData SceneMsg
initData _ _ =
    { objects =
        [ Ship.component (ShipInitMsg <| ShipInit.InitData 0 ( 100, 500 ) 200)
        , Enemy.component (EnemyInitMsg <| EnemyInit.InitData 1 (-1 / 10) ( 1920, 800 ) 120 30 200)
        ]
    , level = "Level1"
    }

init : RawSceneProtoLevelInit UserData SceneMsg (InitData SceneMsg)
init _ env msg =
    Just (initData env msg)

scene : SceneStorage UserData SceneMsg
scene =
    genScene init
```

:::note
`LevelComponentStorage` is `Runtime -> Env cdata userdata -> AbstractComponent`. The actual component creation (applying `runtime` and `env`) happens in the sceneproto's `init`, not in the level. The level only provides the partially-applied storage functions.
:::
