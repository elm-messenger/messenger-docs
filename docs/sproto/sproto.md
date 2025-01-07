---
sidebar_position: 1
---

# Scene Prototype

It's common that there are multiple scenes and their contents are similar. For example, in the game [Reweave](https://github.com/linsyking/Reweave/), there are many _levels_ and all of them are scenes with very similar functionalities. Do we really need to create a separate scene for every level?

Messenger scenes are flexible enough to make a scene virtual, or a **prototype**. Here, prototype means that users can create a real scene by calling a `genScene` function in the prototype by giving it some parameters it needs.

Take _Reweave_ as an example, all the game levels are generated from a single `SceneProto`. The data used to instantiate a scene prototype includes all the components, background items, player data, etc.

`SceneProto` is so useful that Messenger core provides some type sugar and helper functions, and Messenger CLI provides a handy command to create scene prototypes (sceneproto in short).

All the CLI commands we used before may add an extra `--proto` or `-p` argument to indicate that this scene/layer/component is created in `SceneProtos` directory instead of `Scenes` directory.

Besides, CLI adds a new command called "level". Users can create levels of sceneprotos:

```bash
messenger level <SceneProto Name> <Level Name>
```

This will create a scene added to `Scenes` directory and `AllScenes.elm` file.
