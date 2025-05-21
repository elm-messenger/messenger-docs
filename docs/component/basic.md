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

So for a single component, it works just like the general model.

The parameter `data` represents the data type specific to this component type. It is unique and can be freely defined by the user. `bdata`, which stands for **Base Data**, represents the type that is shared among all component types in a set.

Note that common data is shared among all components, so every component can view or modify the single copy of the common data. In contrast, base data is data that every component instance has, but only the type is shared.

For instance, in a platform game, common data may include gravity while base data may include position and velocity. As the value of gravity is same for all components but each component instance can have different values for position and velocity.

The `render` type is aliased to `(Renderable, Int)`. The second entry is the [z-index](https://developer.mozilla.org/en-US/docs/Web/CSS/z-index) property of the component. Each component may have different z-index value.

:::note
It is important understand the concept of _abstract_ and _concrete_ mentioned in [General Model](../intro/model.md#general-model) part when working with components. 

Users must manually manage several lists of components in _abstract type_, while maintaining the logic of different component types separately at the _concrete level_. The lists themselves represent the component instances. 

A good understanding of these relationships will help users use components more effectively.
:::