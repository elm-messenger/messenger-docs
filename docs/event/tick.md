---
sidebar_position: 4
---

# Tick Event

The parameter is `delta: Int`. It is triggered every `timeInterval` (defined in user configuration file). `delta` is the time elapsed since last `Tick` in milliseconds.

`timeInterval` has type of `TimeInterval`, which is defined as:

```elm
type TimeInterval
    = Fixed Float
    | Animation
```

`Fixed` represents the fixed time interval between every two frames. The value is the time interval in milliseconds.

`Animation` will use the browser's `requestAnimationFrame` to update the game. The frame rate will be dependent on your device. This will make the animation looks smoother.

If users want to know the current timestamp, they can access it from `globalData.currentTimeStamp`. Users can convert it to timestamp in milliseconds by `Time.posixToMillis`.

:::note
If an event of the game runs longer than `timeInterval`, all subscriptions will not be triggered until that event finishes.
:::
