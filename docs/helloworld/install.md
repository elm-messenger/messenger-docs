---
sidebar_position: 1
---

# Installation

To create a simple game in Messenger, the first step is to install the [Messenger CLI tool](https://github.com/elm-messenger/Messenger).

Messenger CLI is written in Python. You need to install `python>=3.7`.
It is recommended to use a python package manager instead of `pip` on Unix-based OS (including WSL).
For example, [pipx](https://pipx.pypa.io/stable/) is a tool to manage python applications.
[uv](https://github.com/astral-sh/uv) is also a fast python package manager.

```bash
# pipx:
pipx install -i https://pypi.python.org/simple elm-messenger>=0.5.1
# uv:
uv tool install -i https://pypi.python.org/simple elm-messenger>=0.5.1
# Or use pip on Windows:
pip install -i https://pypi.python.org/simple elm-messenger>=0.5.1
```

This tool is helpful in quickly building a project. To create a new project, use the following commands:

```bash
messenger init helloworld
# Or with custom repo:
messenger init helloworld -t <Repo URL>
# With custom branch:
messenger init helloworld -t <Repo URL> -b <Branch name>
```

By default, it uses a local REGL JS backend with default font (~200KiB).

You could switch to use JS on CDN or use the local version without default font:

```bash
# Use CDN REGL JS with default font:
messenger init helloworld --use-cdn
# Use local REGL JS with no font (~130KiB):
messenger init helloworld --min
# Use CDN REGL JS with no font:
messenger init helloworld --use-cdn --min
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

:::tip
By default, a scene is a `LayeredScene`. To create a `RawScene` without any layers, add the `--raw` argument when creating a scene. Raw scene doesn't have `SceneBase.elm` when created. However, if users try to add a component or a layer to a raw scene, that file will automatically be created.

The functionality of each Elm file created by the template will be explained later.
:::

See [CLI documentation](../misc/som#messenger-cli-commands) to learn more about Messenger CLI.
