---
sidebar_position: 4
---

# Tick Event

## `Tick`

**Definition:** `Tick Float`

Triggered repeatedly every `timeInterval` (defined in user configuration file). 

The parameter represents the time elapsed `delta`, since last `Tick` in milliseconds.

The `timeInterval` has type `REGL.TimeInterval`, which is defined as:

```elm
type TimeInterval
    = AnimationFrame
    | Millisecond Float
```

- `AnimationFrame` uses the browser's `requestAnimationFrame` to update the game instead of manually configuring the time interval. The frame rate depends on the device, resulting smoother animations.

- `Millisecond` represents a fixed time interval between two frames. The value specifiess the time interval in milliseconds. If an event of takes longer to execute than the configured time interval, all subscriptions will not be triggered until that event finishes.

:::note
`delta` is not always equal to the configured time interval. Always use `delta` for time-related updates to get the actual time elapsed.
:::

:::note
Users can use `globalData.currentTimeStamp` to get the current timestamp
:::
