---
sidebar_position: 2
---

# Messenger CLI Commands

You can also use `messenger <command> --help` to view help.

## Init

Initialize a Messenger project.

Usage: `messenger init [OPTIONS] NAME`

Arguments:

- `name`: The name of the project
- `--template-repo`, `-t`: Use a customized repository for cloning templates. Default: `https://github.com/elm-messenger/messenger-templates`
- `--template-tag`, `-b`: The tag or branch of the repository to clone.
- `--auto-commit`, `-g`: Automatically commit template codes.
- `--use-cdn`: Use jsdelivr CDN for elm-regl JS file.
- `--min`: Use minimal regl JS that has no builtin font.
- `--current-dir`, `-c`: Create the project in the current directory.

:::tip
When `--current-dir` is used, the `name` argument is still required but is not used as the directory name.
:::

## Scene

Create a scene. Without `--raw`, the scene is created as a **LayeredScene** (with layer support).

Usage: `messenger scene [OPTIONS] NAME`

Arguments:

- `name`: The name of the scene
- `--raw`: Use raw scene without layers
- `--proto`, `-p`: Create a sceneproto
- `--init`, `-i`: Create an `Init.elm` file

:::note
`--raw` and `--proto` can be used together to create a raw scene prototype.
:::

## Layer

Create a layer.

Usage: `messenger layer [OPTIONS] SCENE LAYER`

Arguments:

- `scene`: The name of the scene
- `layer`: The name of the layer
- `--with-component`, `-c`: Use components in this layer
- `--cdir`, `-cd`: Directory of components in the scene. Default: `Components`
- `--proto`, `-p`: Create layer in sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Component

Create a component.

Usage: `messenger component [OPTIONS] SCENE NAME`

Arguments:

- `scene`: The name of the scene
- `name`: The name of the component
- `--cdir`, `-cd`: Directory to store components. Default: `Components`
- `--proto`, `-p`: Create component in sceneproto
- `--init`, `-i`: Create an `Init.elm` file

## Level

Create a level from a scene prototype.

Usage: `messenger level SCENEPROTO NAME`

Arguments:

- `sceneproto`: The name of the sceneproto
- `name`: The name of the level

## Global Component

Create a global component.

Usage: `messenger gc NAME`

Arguments:

- `name`: The name of the component

## Sync

Sync your project with the latest templates and dependencies from remote repositories. Updates Elm packages, JavaScript files (`elm-audio.js`, `elm-messenger.js`, `regl.js`), and `elm.json` to match the latest versions.

Usage: `messenger sync [OPTIONS]`

Arguments:

- `--force`, `-f`: Force sync, disable all checks and replace dependencies in `public/` forcibly.
- `--template-repo`, `-t`: The new repository to sync from, empty for no change.
- `--template-tag`, `-b`: The new tag or branch to sync from, empty for no change.
- `--list`, `-l`: List the current dependencies version and latest version on remote without making changes.

:::note
`--list` exits with code 1 when updates are available.
:::

## Remove

Remove a scene or a sceneproto.

Usage: `messenger remove [OPTIONS] TYPE NAME`

Arguments:

- `type`: Either `"scene"` or `"sceneproto"`. No other values are accepted.
- `name`: The name of the scene or sceneproto
- `--rm`: Also remove the module source files from disk
- `--rml`: Remove all levels in the sceneproto

:::note
`--rml` removes the level entries from `messenger.json`. Combined with `--rm`, it also deletes each level's source directory from disk.
:::

## Font

Install custom fonts for use in your Messenger project.

Usage: `messenger font [OPTIONS] <font_file> [-n <name>] [-i <charset_file>] [-s <font_size>] ...`

Each font is specified by a font file path, optionally followed by:
- `-n <name>`: Custom name for the font (defaults to the file stem)
- `-i <charset_file>`: Character set file for the font
- `-s <font_size>`: Font size (default: 40)

Options:

- `--range`: Set the distance range. Default: 4

:::tip
Font files are not validated before the confirmation prompt. Make sure the file paths are correct before proceeding.
:::

See [text rendering](../rendering/text.md).
