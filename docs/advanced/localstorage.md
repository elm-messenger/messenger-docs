---
sidebar_position: 1
---

# LocalStorage

[Local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) is a mechanism to store data in the browser. It allows the game to save data locally.

In Messenger, local storage content is defined by a `String`. When users need to save something to local storage, they need to first serialize it (for example, use `Json`).

Users can read or write to local storage by editing `globalData.userData` and the data type in local storage is defined in `Lib/UserData.elm`. However, not all things in `UserData` are needed to store to local storage.

Users will need to implement `initGlobalData` and `saveGlobalData` functions. They act as a decoder and encoder for global data.

`initGlobalData` is called when the game starts.
Its type is:

```elm
initGlobalData : String -> GlobalDataInit UserData
```

The string it needs is the real local storage data. Users will use the local storage data to initialize writable global data and initial volume.

`GlobalDataInit` contains `camera`, `volume`, `extraHTML`, `canvasAttributes`, and `userData`. Runtime fields such as timestamps, pressed keys, current scene, loaded resources, and mouse position are initialized and maintained by Messenger.

See [Global Data and Getters](../intro/globaldata) for the exact writable fields and read-only getters.

`saveGlobalData` is called when the user wants to save the global data (emitted by a `SOMSaveGlobalData` message). Users may encode some part of global data.

```elm
saveGlobalData : Runtime -> GlobalData UserData -> String
```

`saveGlobalData` receives the current read-only `Runtime` and the full writable `GlobalData`. If you need engine-owned values while saving, read them from `runtime` with getters.

:::tip
Also see [tetris](https://github.com/elm-messenger/messenger-core/tree/main/test/src/Scenes/Tetris) example to learn how to use localStorage in Messenger. The userdata is defined at [UserData.elm](https://github.com/elm-messenger/messenger-core/blob/main/test/src/Lib/UserData.elm).
:::
