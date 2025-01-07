---
sidebar_position: 1
---

# Installation

To create a simple game in Messenger, the first step is to install the Messenger CLI tool:

Messenger CLI is written in Python. You need to install `python>=3.7`. [pipx](https://pipx.pypa.io/stable/) is a tool to manage python applications. It is recommended to use `pipx` instead of `pip` on Unix-based OS (including WSL).

```bash
pipx install -i https://pypi.python.org/simple elm-messenger>=0.4.0
# Or use pip on Windows:
pip install -i https://pypi.python.org/simple elm-messenger>=0.4.0
```

This tool assists in quickly building a project. To create a new project, use the following commands:

```bash
messenger init helloworld
# Or with custom repo:
messenger init helloworld -t <Repo URL>
# With custom branch:
messenger init helloworld -t <Repo URL> -b <Branch name>
```

Then, create a new scene and add a layer in that scene:

```bash
# Open our project directory
cd helloworld
# Create a new scene
messenger scene Home
# Create a new layer called MainLayer
messenger layer Home MainLayer
```

:::note
If the scene name is not "Home", change the scene name in `initScene` in `MainConfig.elm` (which defines which scene to start), as it is set to "Home" by default.
:::

:::note
It is fine to use lowercase letters like `home` and `mainLayer`. The Messenger CLI will automatically convert these to appropriate names.
:::

:::note
By default, a scene is a `LayeredScene`. To create a `RawScene` without any layers, add the `--raw` argument when creating a scene. Raw scene doesn't have `SceneBase.elm` when created. However, if users try to add a component or a layer to a raw scene, that file will automatically be created.
:::

See [CLI documentation](../misc/som#messenger-cli-commands) to learn more about Messenger CLI.
