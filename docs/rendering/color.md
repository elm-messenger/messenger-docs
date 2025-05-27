---
sidebar_position: 3
---

# Color

Elm-regl uses premultiplied alpha rendering mode in WebGL to create more accurate rendering and compositing.

To use this, you only need to notice that when you are specifying RGBA colors in rendering commands, always multiply RGB by alpha value beforehand, and the RGBA value should follow:

$$
R, G, B\leq A
$$

## Why pre-multiplying?

Think what "alpha" value really is. How to draw a transparent color on the screen? Without premultiplied alpha mode, the renderer needs to multiply RGB values by its alpha channel.

However, in premultiplied alpha mode, the alpha channel is not needed as the color is already updated. Therefore the alpha channel now stores the "real" alpha value that is used with blending.

For example, we have two colors, one in background (B) and one in foreground (F):

With premultiplied alpha:

$$
C = F.RGB + B.RGB \cdot (1 - F.A)
$$

Without premultiplied alpha:

$$
C = F.RGB \cdot F.A + B.RGB \cdot (1 - F.A)
$$

This is less accurate.
