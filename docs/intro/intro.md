---
sidebar_position: 1
---

# Introduction

There are several Elm packages like [elm-playground](https://package.elm-lang.org/packages/evancz/elm-playground/latest/), which offer simple APIs to create a game. However, these packages have many limitations and are not suitable for creating complex games.

Messenger is a 2D game engine for Elm based on `elm-canvas`. It provides an architecture, a set of APIs, and many library functions to enable rapid game development. Additionally, Messenger is message-based and abstracts the concept of objects using the _co-inductive type_ technique.

Messenger has many cool features:

- **Coordinate System Support**  
  The viewport and coordinate transformation functions are already implemented. Messenger is also adaptive to window size changes.

- **Separate User Code and Core Code**  
  User code and core code are separated. Any side effects are controlled by the Messenger core. This helps debugging and decreases security concerns.

- **Basic Game Engine API Support**  
  Messenger provides handy common game engine APIs, including data storage, an audio manager, a sprite(sheet) manager, and more. Additional features are under development.

- **Modular Development**  
  Every component, layer, and scene is a module, simplifying code management. The implementation is highly packaged, allowing focus on the specific logic of the needed functions. Messenger is designed for convenience and ease of use.

- **Highly Customizable**  
  The data of each object can be freely defined. The target matching logic and message type are also customizable. Users can create their own object types using the provided General Model type.

- **Flexible Design**  
  The engine can be used to varying degrees, with separate management of different tasks. Components can be organized flexibly using the provided functions, allowing classification of portable and non-portable components in any preferred way.
