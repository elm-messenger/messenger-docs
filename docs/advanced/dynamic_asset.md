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

You could check if all assets are loaded by `getLoadingProgress runtime`.

All assets are loaded into opaque runtime data. Use getters such as `getSprite`, `getAllSprites`, `getFonts`, `getPrograms`, `getConfigData`, and `getLoadingProgress` instead of directly inspecting runtime internals.
