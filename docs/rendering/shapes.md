---
sidebar_position: 3
---

# Shapes

Elm REGL provides several builtin commands to draw shapes. All those commands are in `REGL.BuiltinPrograms`.

## Triangle

Definition: `triangle : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable`.

## Rectangle

Definition 1: `quad : ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable`. (Slow, **Not Recommended**)

Definition 2: `rect : ( Float, Float ) -> ( Float, Float ) -> Color -> Renderable`.

`rect` uses left-top coordinate and size to draw the rectangle.

`rect` will call `rectCentered` because it is faster.

Definition 3: `rectCentered : ( Float, Float ) -> ( Float, Float ) -> Float -> Color -> Renderable`. (Recommended)

`rectCentered` use center coordinate, size and angle to draw the rectangle.

## Polygon

Definition: `poly : List ( Float, Float ) -> Color -> Renderable`.

By default, it uses `triangles` [OpenGL primitive](https://www.khronos.org/opengl/wiki/primitive). In case you need other primitives, use `polyPrim`.

## Lines

Definition: `lines : List ( ( Float, Float ), ( Float, Float ) ) -> Color -> Renderable`.

By default, it uses `lines` primitive, for other primitives, use `linestrip` or `lineloop`.

## Curves

Currently there is no builtin curve support in elm-regl, but users could easily implement one by sampling the points on the curve.

An example is `functionCurve` function in the library.

## Circle

Definition: `circle : ( Float, Float ) -> Float -> Color -> Renderable`.

Given the center and radius, it draws a circle. Note that the circle is anti-aliased by default.