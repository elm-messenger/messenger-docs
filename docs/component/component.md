---
sidebar_position: 1
---

# Components

Components can be considered as the basic elements in the game. Every layer contains several components and each type of component has its own logic.

Components are versatile. Users can abstract almost every single thing in their game as a component. For instance, a character that can move and attack, the bullet fired by the character, a settings panel, an abstract concept like "ability to cast magic", and a manager to manage one specific type of components. You may even embed components into a component! Properly implementing objects and abstract concepts as components can greatly reduce your workload. 

Components are customizable. Users can easily define all the details of a component. One component type can have its unique data type, matcher, initialization, update and render.

Components are flexible. Users can organize different types of components whatever they like. They can set one component type be a sub-type of other component. They can also put different type of components into one set, so that the components can share a basic data type, and send the same type of messages to each other easily. Users can always put components whose types are in the same set, into one list in layer.

Using components allows users to simplify a complex feature and manage it step by step. Moreover, it provides a simpler and more flexible way for users to organize code. So it is necessary to use components to implement most of the logics for a complex game.
