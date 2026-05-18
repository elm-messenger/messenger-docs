---
sidebar_position: 6
---

# Config Data

Messenger supports loading plain text files (such as JSON, CSV, or any text-based configuration) as a resource type, similar to textures and fonts. This is useful for data-driven game design such as level layouts, enemy stats, dialogue trees, etc.

Users can add their configuration in `src/Lib/Resources.elm` using `DataRes`, like:

```elm
allData : ResourceDefs
allData =
    [ ( "level1", DataRes "assets/data/level1.json" )
    , ( "enemyStats", DataRes "assets/data/enemies.csv" )
    ]
```

`DataRes` takes the file path as argument. 

:::note
Files are fetched as plain text. Messenger does not parse or interpret the content.
:::

Once loaded, data files can be read with `getConfigData`:

```elm
import Messenger.Base exposing (getConfigData)

case getConfigData "level1" runtime of
    Just raw ->
        -- raw is the file content as a String, decode it yourself
        ...

    Nothing ->
        ...
```

Users should provide a decoder according to the file type to extract needed data from raw string. 
