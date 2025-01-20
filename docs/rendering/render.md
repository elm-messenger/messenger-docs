---
sidebar_position: 1
---

# Rendering

As mentioned before, Messenger uses _virtual coordinates_ to render. The transformation from virtual coordinates to real coordinates are mostly done in the shaders.
The virtual coordinate system is supported by elm-regl, which makes the rendering process simpler for Messenger.

:::tip
If necessary (**very rare though**), users can use functions `posToReal`, `lengthToReal` from `Messenger.Coordinate.Coordinates` to manually transform coordinates.
:::

Messenger does not expose a camera system because currently it is still targeting 2D games. Therefore users need to implement the camera on demand.

The drawing style of Messenger is rather _declarative_. Declarative rendering has many benefits:

- Simple to read, debug and understand
- Allow transparent abstraction of layers

A famous example of declarative drawing API is SVG. However, SVG is only for vector graphics and it performs bad.

Messenger's rendering backend, elm-regl, uses a custom declarative language called **DRDL** (Declarative REGL Drawing Language). The elm-regl library generates DRDL from the elm side and passes it to elm REGL JS port.
The elm REGL JS will handle it and renders.

Users do not need to touch the low-level DRDL. Instead, elm-regl library provides the abstraction.

## Basic concepts

Elm-regl defines a basic type `Renderable`. Renderable is an abstraction of some drawings on the screen.

A REGL command is a function that generates a renderable.

For example, the command to draw a triangle is defined as:

```elm title="REGL/BuiltinPrograms.elm"
triangle : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable
```

Users need to provide three coordinates and a color to draw a triangle. Remember that the `view` function of Messenger general model accepts a renderable, so it is very easy to draw anything in Messenger.

## Structural Drawing

The most important feature of declarative drawing is _structural drawing_. It allows users to group a list of renderables:

```elm title="REGL.elm"
group : List Effect -> List Renderable -> Renderable
```

The first argument is a list of effects users want to apply for all the renderables. See [effects](effects) for more details.

`group [] [a, b]` draws `a` first, then draws `b`. Elm REGL does not enable depth test so the order of rendering determines which is on the top.

Structural drawing allows users to divide a big scene into several "layers" and render them in different groups.

:::tip
To render nothing, please use `REGL.empty` instead of `REGL.group [] []`.
:::

## First Drawing Command

A common REGL drawing command is `clear`, which is to reset the colors on screen. `clear` also generates a renderable, so usually users write:

```elm
import REGL
import REGL.BuiltinPrograms as P
...
REGL.group [] [
    P.clear Color.white,
    ...
]
```