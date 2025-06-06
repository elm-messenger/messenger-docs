---
sidebar_position: 1
---

# LocalStorage

[Local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) is a mechanism to store data in the browser. It allows the game to save data locally.

In Messenger, local storage content is defined by a `String`. When users need to save something to local storage, they need to first serialize it (for example, use `Json`).

Users can read or write to local storage by editing `globalData.userdata` and the data type in local storage is defined in `Lib/UserData.elm`. However, not all things in `UserData` are needed to store to local storage.

Users will need to implement `initGlobalData` and `saveGlobalData` functions. They act as a decoder and encoder for global data.

`initGlobalData` is called when the game starts.
Its type is:

```elm
initGlobalData : String -> UserViewGlobalData UserData
```

The string it needs is the real local storage data. Users will use the local storage data to initialize global data.

`saveGlobalData` is called when the user wants to save the global data (emitted by a `SOMSaveGlobalData` message). Users may encode some part of global data.

:::tip
Also see [tetris](https://github.com/elm-messenger/messenger-core/tree/main/test/src/Scenes/Tetris) example to learn how to use localStorage in Messenger. The userdata is defined at [UserData.elm](https://github.com/elm-messenger/messenger-core/blob/main/test/src/Lib/UserData.elm).
:::