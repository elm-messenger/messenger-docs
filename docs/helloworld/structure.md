---
sidebar_position: 2
---

# Project Structure

You should be expected to see the following structure:

```bash
./helloworld
├── assets # Store game assets in this folder
├── elm.json
├── index.html
├── Makefile
├── messenger.json
├── .messenger # Data used by Messenger, don’t modify this by hand!
├── public # JS, CSS and HTML files
└── src
    ├── Lib
    │   ├── Base.elm # Base types for many modules
    │   ├── Ports.elm
    │   ├── Resources.elm # Images to load
    │   └── UserData.elm # User global data
    ├── MainConfig.elm # Configurations for the game
    ├── Main.elm
    └── Scenes # Scene folder
        ├── AllScenes.elm # Stores all the scene data
        ├── GlobalComponents.elm # Stores all the global components
        └── Home # A scene called "Home"
            ├── MainLayer # A layer called "MainLayer" in Home
            │   ├── Model.elm # Layer definitions
            │   └── Init.elm # Init message type
            ├── Components # Scene components
            │   ├── Comp # A component called "Comp"
            │   │   ├── Model.elm
            │   │   └── Init.elm
            │   └── ComponentBase.elm
            ├── SceneBase.elm
            └── Model.elm
```
