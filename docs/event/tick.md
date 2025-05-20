---
sidebar_position: 4
---

# Tick Event

## `Tick`

**Definition:** `Tick Float`

Triggered repeatedly every `timeInterval` (defined in user configuration file). 

The parameter represents the time elapsed `delta`, since last `Tick` in milliseconds. Messenger use [`performance.now()`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now)(if available) to deal with times, so the value is with up to microsecond precision. 

The `timeInterval` has type `REGL.TimeInterval`, which is defined as:

```elm
type TimeInterval
    = AnimationFrame
    | Millisecond Float
```

- `AnimationFrame` uses the browser's `requestAnimationFrame` to update the game instead of manually configuring the time interval. The frame rate depends on the device, resulting smoother animations.

- `Millisecond` represents a fixed time interval between two frames. The parameter specifiess the time interval in milliseconds.

:::note
`delta` is not always equal to the configured time interval, since there might be fluctuations and timeouts. Always use `delta` for time-related updates to get the actual time elapsed.
:::

:::note
Users can use `globalData.currentTimeStamp` to get the current timestamp.
:::

## Update and Render Model

In Messenger, rendering does not correspond directly to a single game update. Instead, an event queue is maintained, where each event triggers a game update. After all events in the current queue are processed, rendering occurs. While the game is updating and rendering, any new incoming events are added to a new queue, which will be handled in the next cycle, rather than being added to the current queue. The `Tick` event is treated like any other event, except that it is triggered repeatedly.

:::tip
Users do not need to worry about the correctness of the model state when many events occur, as the model is updated according to the event queue. Though the game will be shown as paused since rendering is blocked.
:::

When the time interval is set to `Millisecond` mode, both the `Tick` event and rendering are triggered at the configured interval. If a frame takes longer than the configured interval, the next `Tick` will wait until the current frame finishes.

![](/img/event1.jpg)

When the time interval is set to `AnimationFrame` mode, the `Tick` event and rendering are triggered as soon as the previous updates are complete.

![](/img/event2.jpg)
