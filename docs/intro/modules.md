---
sidebar_position: 2
---

# Messenger Modules

There are several modules (sub-projects) within the Messenger project. All development of Messenger is on GitHub:

- [Messenger CLI](https://github.com/elm-messenger/Messenger): A handy CLI to create games rapidly
- [Messenger core](https://github.com/elm-messenger/messenger-core): Core Messenger library
- [Messenger extra](https://github.com/elm-messenger/messenger-extra): Extra Messenger library with experimental features
- [Messenger templates](https://github.com/elm-messenger/messenger-templates): Templates to use the Messenger library, used in the Messenger CLI
- [Elm REGL](https://github.com/elm-messenger/elm-regl): Rendering backend

:::note
This doc is compatible with core `20.0.0`, elm-regl `10.0.0`, templates and CLI `0.6.0`.
:::

## Update Messenger

If you are using a messenger version that is not listed here, you may update your messenger and your messenger project with the following steps:

**Install latest Messenger CLI**

```bash
pipx install -i https://pypi.python.org/simple elm-messenger>=0.6.0
```

**Remove `.messenger` in your messenger project**

```bash
cd <your project directory with .messenger>
rm -rf .messenger
```

**Upgrade Messenger libraries**

Edit your `elm.json`, change the following packages to the latest version:

```json
"linsyking/elm-regl": "10.0.0",
"linsyking/messenger-core": "20.0.0"
```

Then run `make`!
