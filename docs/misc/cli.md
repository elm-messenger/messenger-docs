---
sidebar_position: 2
---

# Messenger CLI Commands

You can also use `messenger <command> --help` to view help.

## Scene

Create a scene.

Usage: `messenger scene [OPTIONS] NAME`

Arguments:

- `name`: The name of the scene
- `--raw`: Use raw scene without layers
- `--proto`, `-p`: Create a sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Init

Initialize a Messenger project.

Usage: `messenger init [OPTIONS] NAME`

Arguments:

- `name`: The name of the project
- `--template-repo`, `-t`: Use a customized repository for cloning templates.
- `--template-tag`, `-b`: The tag or branch of the repository to clone.
- `--use-cdn`: Use jsdelivr CDN for elm-regl JS file.
- `--min`: Use minimal regl JS that has no builtin font.

## Layer

Create a layer.

Usage: `messenger layer [OPTIONS] NAME LAYER`

Arguments:

- `name`: The name of the scene
- `layer`: The name of the layer
- `--with-component`, `-c`: Use components in this layer
- `--cdir`, `-cd`: Directory of components in the scene
- `--proto`, `-p`: Create layer in sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Level

Create a level.

Usage: `messenger level [OPTIONS] SCENEPROTO NAME`

Arguments:

- `sceneproto`: The name of the sceneproto
- `name`: The name of the level

## Component

Create a component.

Usage: `messenger component [OPTIONS] SCENE NAME`

Arguments:

- `scene`: The name of the scene
- `name`: The name of the component
- `--cdir`, `-cd`: Directory to store components
- `--proto`, `-p`: Create component in sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Global Component

Create a global component.

Usage: `messenger gc [OPTIONS] NAME`

Arguments:

- `name`: The name of the component

## Update

Usage: `messenger update`

Update `messenger.json` based on existing files.

## Remove

Remove a scene or a sceneproto.

Usage: `messenger remove [OPTIONS] TYPE NAME`

Arguments:

- `type`: Either `"scene"` or `"sceneproto"`
- `name`: The name of the scene or sceneproto
- `--rm`: Also remove the modules
- `--rml`: Remove all levels in the sceneproto
- `--help`: Show this message and exit

## Font

Generate font assets.

Usage: `messenger font [TEXTURE_OPTIONS] [FONT FONT_OPTIONS]..`

See [text rendering](../rendering/text.md).
