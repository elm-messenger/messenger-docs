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
pipx install -i https://pypi.python.org/simple elm-messenger>=0.5.3
# uv:
uv tool install -i https://pypi.python.org/simple elm-messenger>=0.5.3
# Or use pip on Windows:
pip install -i https://pypi.python.org/simple elm-messenger>=0.5.3
```

This tool is helpful in quickly building a project. To create a new project, use the following commands:

```bash
messenger init helloworld
# Or with custom repo:
messenger init helloworld -t <Repo URL>
# With custom branch:
messenger init helloworld -t <Repo URL> -b <Branch name>
```

By default, it uses a local REGL JS backend with default font `consolas` (~200KiB).

You could switch to use JS on CDN or use the local version without default font:

```bash
# Use CDN REGL JS with default font:
messenger init helloworld --use-cdn
# Use local REGL JS with no font (~140KiB):
messenger init helloworld --min
# Use CDN REGL JS with no font:
messenger init helloworld --use-cdn --min
```

See [CLI documentation](../misc/som#messenger-cli-commands) to learn more about Messenger CLI.
