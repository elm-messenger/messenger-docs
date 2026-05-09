---
sidebar_position: 7
---

# Dynamic Asset Loading

:::warning
Experimental feature. Small games should load all game assets at the beginning of the game.
:::

Messenger provides a SOM call `SOMLoadResource` to load resources at runtime.

The definition is:

```elm
SOMLoadResource String ResourceDef
```

It is the same as you define your font, texture, audio, or  config data assets.

You could check if all assets are loaded by `globalData.internalData.loadedResNum == globalData.internalData.totResNum`.

All the assets are loaded in `internalData`, so it is also possible to directly inspect the `internalData`.
