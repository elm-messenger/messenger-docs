---
sidebar_position: 4
---

# Order of Rendering

To understand layers and scenes better, let’s create another scene called "Game" and add two layers called "A" and "B" in that scene.

```bash
# Create a new scene
messenger scene Game
# Create new layers
messenger layer Game A
messenger layer Game B
```

Now similarly, add these two layers to scene:

```elm
...
    , layers =
        [ A.layer NullLayerMsg envcd
        , B.layer NullLayerMsg envcd
        ]
```

Then draw a red rectangle on layer A and a blue rectangle on layer B at the same position. See the result.

Layer B will be on the top of the layer A, which means A is rendered before B. The order is the same when using `Canvas.group`. The first element in a list will be rendered first and be at the bottom of the scene. Therefore, it is important to organize the order of layers correctly.

However, the order of updating is different from rendering. See [Events](../event).
