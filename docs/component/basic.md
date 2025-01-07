---
sidebar_position: 2
---

# Basic Model

Basically the component type is inherited from the general model as follows:

```elm
type alias ConcreteUserComponent data cdata userdata tar msg bdata scenemsg =
    ConcreteGeneralModel data (Env cdata userdata) UserEvent tar msg ( Renderable, Int ) bdata (SceneOutputMsg scenemsg userdata)
type alias AbstractComponent cdata userdata tar msg bdata scenemsg =
    AbstractGeneralModel (Env cdata userdata) UserEvent tar msg ( Renderable, Int ) bdata (SceneOutputMsg scenemsg userdata)
```

So for a single component, it works just like how general model does.

The parameter `data` represents the data type of this type of component. It is unique since users can set it freely. `bdata`, which stands for **Base Data**, represents the type that will be the same among all the types of components in one set.

Note the common data is the data that shared among all the components, so every component can view or change the only copy of the common data. However, base data is the data that every component has, and only the type is shared.

For instance, in a platform game, common data may include gravity while base data may include positions and velocities.

The `render` type is aliased to `(Renderable, Int)`. The second entry is the [z-index](https://developer.mozilla.org/en-US/docs/Web/CSS/z-index) property of the component. Each component may have different z-index value.
