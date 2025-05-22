---
sidebar_position: 3
---

# User Components

User components are the components that are mostly used. A user component should be attached to a specific scene, then it can only be used in that scene.

To understand the usage of the components better, let's make an example.

```bash
# Create a new scene named Game
messenger scene Game

# Create a new type of component in Game scene
messenger component Game Comp

# Create a new layer with components in Game Scene
messenger layer -c Game A
```

In addition to setting the data type of `Comp`, it is necessary to set the data type for initializing a `Comp`. Since we would like to determine the position, size and color when initializing, add codes in `Scenes/Home/Components/ComponentBase.elm`:

```elm
type alias Init =
    { left : Float
    , top : Float
    , width : Float
    , height : Float
    , color : Color
    }
type alias Data =
    { left : Float
    , top : Float
    , width : Float
    , height : Float
    , color : Color
    }
```

Then add the message type for initialization in `ComponentMsg`.

However, the data set in `ComponentBase.elm` will be applied to all types of components. So it is a choice to set the init data and message type for every single type of component. Users can create a file named `Init.elm` in `Scene/Home/Components/Comp` to store them separately. Users can also use the option `--init` or `-i` to do this when creating the component. Don't forget to import it in `ComponentBase.elm`.

```bash
messenger component -i Game Comp
```

Then draw a rectangle based on the component data in the `view` function. But it will not be rendered on screen now since it has not been added to any layer yet. So let's add two components when initializing the layer and render them using `viewComponents`:

```elm
import Scenes.Home.Components.Comp.Model as Rect
import Scenes.Home.Components.Comp.Msg as RectMsg
...
type alias Data =
    { components : List (AbstractComponent SceneCommonData UserData ComponentTarget ComponentMsg BaseData SceneMsg)
    }
init : LayerInit SceneCommonData UserData LayerMsg Data
init env initMsg =
    Data
        [ Rect.component ( RectangleInit <| RectMsg.Init 150 150 200 200 Color.blue ) env
        , Rect.component ( RectangleInit <| RectMsg.Init 200 200 200 200 Color.red ) env
        ]
...
view : LayerView SceneCommonData UserData Data
view env data =
    viewComponents env data.components
```

Note that `Data` and `view` function has been provided.

Now one red rectangle on a blue one on screen.

Then try to add a simple logic that when you left-click the rectangle, it turns black:

```elm
update : ComponentUpdate SceneCommonData Data UserData SceneMsg ComponentTarget ComponentMsg BaseData
update env evnt data basedata =
    case evnt of
        MouseDown 0 pos ->
            if judgeMouseRect pos ( data.left, data.top ) ( data.width, data.height ) then
                ( ( { data | color = Color.black }, basedata ), [], ( env, False ) )

            else
                ( ( data, basedata ), [], ( env, False ) )

        _ ->
            ( ( data, basedata ), [], ( env, False ) )
```

Then update all the components in the layer using `updateComponents`, which has been done in `A/Model.elm` by default.

## Message Blocking

Our code seems to work well when we click the non-overlapping part of the two rectangles. But when we click the overlapping part of them, both of them turn black, which is not expected. The issue has been mentioned in [events](../event). So we can solve this problem by changing the block value from `False` to `True`.

```elm
( ( { data | color = Color.black }, basedata ), [], ( env, True ) )
```

What if we add a layer `B` to the scene? Create a new layer B:

```bash
messenger layer -c Game B
```

Then add it to the scene. Note to put `B` before `A` in the layer list so that layer `A` will update before `B` and render after `B`. See [layers](../helloworld/order) and [events](../event).

Add two rectangle components to `B` in position (100, 100) and (250, 250) with size (200, 200), which means they will be overlapped by the components in `A`. Left-click one component in `A`, the components in `B` do not turn black. It shows the block indicator will be passed by the order of updating.

## Message Communication

The work we did until now is just to update the component itself. But how to communicate with other components and layers? Let's add a logic that when the user clicks one rectangle, the color of the other one turns green.

First of all, setting a message type in `Components/Comp/Init.elm` is necessary. Since the only message needed to pass is the color to change, add:

```elm
type alias Msg =
    Color
```

Then add it to `ComponentMsg` in `ComponentBase.elm`. How a component reacts to the messages is determined in `updaterec`:

```elm
updaterec : ComponentUpdateRec SceneCommonData Data UserData SceneMsg ComponentTarget ComponentMsg BaseData
updaterec env msg data basedata =
    case msg of
        RectangleMsg c ->
            ( ( { data | color = c }, basedata ), [], env )

        _ ->
            ( ( data, basedata ), [], env )
```

Since the message is going to be sent from one component to the other, the target and matcher system need to be improved. An id can be added for every component to identify themselves. Let's add `id : Int` in `Data` and `InitData`. Then change the `ComponentTarget` type into `Int`, and modify the `matcher`:

```elm
matcher : ComponentMatcher Data BaseData ComponentTarget
matcher data basedata tar =
    tar == data.id
```

The `update` function also needs to be updated so that it can send a message to other components when the mouse left-clicks.

```elm
  ( ( { data | color = Color.black }, basedata )
  , List.filterMap
      (\n ->
          if n /= data.id then
              Just <| Other ( n, RectangleMsg green )

          else
              Nothing
      )
    <|
      List.range 0 1   -- use 0 1 because there are just 2 components
  , ( env, True )
  )
```

Run `make` and see the result now!

:::note
When you click a component in one layer, the components in the other layer won't change their colors. So components cannot directly communicate across layers, they have to send messages to each other via their parent layer.
:::

Now the issue is to communicate between layer and component. Sending a message to components from a layer is similar to sending from component. But a layer cannot directly deal with the messages from components in `updaterec` because the `updaterec` function of the layer is used to handle the message from other layers.

![](/img/comp1.jpg)

Therefore, a handler for the messages from components should be added. We can simply modify the handler provided by default:

```elm
handleComponentMsg : Handler Data SceneCommonData UserData LayerTarget LayerMsg SceneMsg ComponentMsg
handleComponentMsg env compmsg data =
    case compmsg of
        SOMMsg som ->
            ( data, [ Parent <| SOMMsg som ], env )

        OtherMsg msg ->
            case msg of
                RectangleMsg color ->
                    let
                        _ =
                            Debug.log "msg" color
                    in
                    ( data, [], env )

                _ ->
                    ( data, [], env )
```
In this way, components and their parent layer can communicate easily.

## z-index 

Notice that the red rectangle is on the top of the blue one now. How to reverse their order to make the blue one on the top? A very simple way is to directly change their order in the initial list in the layer. Since the render order depends on the order in the list by default, the render order reverses in this way.

However, this method doesn't work in some cases, especially when a new component is added during the game or the render order needs to be changed during the game. These are the situations where z-index should be used.

Users can decide the z-index dynamically based on data or environment in the `view` function. Take the previous issue for example, the requirement can be implemented by adding a new value `order` in `data` which determines the z-index.

The source code in this part can be found [here](https://github.com/linsyking/messenger-examples/tree/main/layers).

## Parent Interfaces

In some situations, the parent of components may need to operate on them directly, without message passing, to avoid unnecessary redundancy and latency. For example, deleting a component or accessing its position.

However, since the components are stored as abstract types, the operations available to the parent are limited. Essentially, the parent can only access what the abstract type exposes: `update`, `updaterec`, `view`, `matcher`, `baseData`.

`update`, `updaterec` and `view` are rarely used. The useful interfaces are `matcher`, which is usually used to identify the component, and `baseData`, which allows the parent to access the base data of a component.

Before operating on a component, it should be _unrolled_ since the abstract types are _rolled_ to enable delayed evaluation. Messenger provides an `unroll` function to convert an abstract-typed component into its unrolled form.

For example, to access the `position` value stored in the `baseData` of a component `comp`, users can use:

```elm
(unroll comp).baseData.position
```

To write a function that checks whether a component is of type "Enemy":

```elm
\comp -> (unroll comp).matcher "Enemy"
```

:::note
Most of the time message passing is sufficient for interaction between a component and its parent. Avoid overusing `baseData` for easy accessibility.

Baiscally, base data is designed to pass shared-typed data easily without encoding and decoding. And `baseData` interface is intended for performing operations across all components.
:::