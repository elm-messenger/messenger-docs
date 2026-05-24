---
sidebar_position: 5
---

# Shapes

Elm REGL provides several builtin commands to draw shapes. All those commands are in `REGL.BuiltinPrograms`. Here is a quick example:

```elm
import REGL.BuiltinPrograms exposing (rect, rectCentered, circle, triangle, lines, poly)
import Color exposing (rgba)

r = rect ( 100, 100 ) ( 200, 150 ) (rgba 1 0 0 1)
c = circle ( 400, 300 ) 50 (rgba 0 1 0 1)
```

## Triangle

Definition: 

```elm
triangle : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable
```

## Rectangle

Definition 1 (Slow, **Not Recommended**): 

```elm
quad : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable
```

Definition 2: 

```elm
rect : ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable
```

`rect` uses left-top coordinate and size to draw the rectangle.

`rect` will call `rectCentered` because it is faster.

Definition 3 (Recommended): 

```elm
rectCentered : ( Float, Float ) -> ( Float, Float ) -> Float -> Color -> Renderable
```

`rectCentered` use center coordinate, size and angle to draw the rectangle.

## Polygon

Definition: 

```elm
poly : List ( Float, Float ) -> Color -> Renderable
```

By default, it uses `triangles` [OpenGL primitive](https://www.khronos.org/opengl/wiki/primitive). In case you need other primitives, use `polyPrim`.

## Lines

Definition: 

```elm
lines : List ( ( Float, Float ), ( Float, Float ) ) -> Color -> Renderable
```

By default, it uses `lines` primitive, for other primitives, use `linestrip` or `lineloop`.

## Curves

Currently there is no builtin curve support in elm-regl, but users could easily implement one by sampling the points on the curve.

An example is `functionCurve` function in the library.

## Circle

Definition: 

```elm
circle : ( Float, Float ) -> Float -> Color -> Renderable
```

Given the center and radius, it draws a circle. Note that the circle is anti-aliased by default.
