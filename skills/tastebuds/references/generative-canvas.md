# Generative canvas work

Covers point fields, type fields, and other procedural imagery rendered to
canvas. Useful for hero art, social cards, backgrounds, and posters.

## The basic rig

A field is a grid of points, displaced by a function, projected through a
camera, with something drawn at each point.

```js
for (let r = 0; r < rows; r++) {
  for (let c = 0; c < cols; c++) {
    const x  = (c - cols / 2) * spacingX
    const z0 = (r - rows / 2) * spacingZ

    // displacement
    const y0 = amp * Math.sin(x * k1 + z0 * k2)

    // tilt toward camera
    const y = y0 * Math.cos(pitch) - z0 * Math.sin(pitch)
    const z = y0 * Math.sin(pitch) + z0 * Math.cos(pitch) + camDist
    if (z < near) continue

    const s = focal / z              // perspective scale
    const sx = cx + x * s
    const sy = cy + y * s
    const size = baseSize * s
    // ...draw
  }
}
```

Cull aggressively: skip points off-screen, behind the camera, or below about
1.2px. It is the difference between a fast render and a slow one.

## Legibility in a type field

**This is the part that is not obvious and it is where these designs usually
fail.** Drawing one character per point produces beautiful noise by default. To
make text actually readable along the field, three relationships must hold.

**1. Rows must not collide: `amp < spacingZ`.**
If the displacement is larger than the distance between rows, neighbouring rows
cross in screen space and characters from different lines interleave. No amount
of styling recovers from this.

**2. Rows need their own pitch: `spacingZ >> spacingX`.**
Use separate spacing for columns and rows. Letters sit close together across a
row (they form words), rows sit far apart in depth (they form separate readable
bands with black between them). A single shared spacing collapses the bands into
each other.

A ratio around 3:1 (`spacingX 32`, `spacingZ 94`) gives clearly separated lines.

**3. Spend amplitude on `k1`, not `k2`.**

This is the key insight. The displacement `sin(x * k1 + z * k2)` has two
frequencies and they do very different things:

- **`k1`** controls how fast the wave varies **along a row**. This is the sweep
  of each text line. It stays readable as long as the whole line spans no more
  than about one and a half wave cycles. Amplitude spent here is free: it buys
  visible curve at no cost to legibility.
- **`k2`** controls the phase difference **between neighbouring rows**. This is
  what creates the diagonal ribbon sweep, and it is also what makes rows
  collide. Amplitude spent here is expensive.

So: **raise `amp` and lower `k2`** for more visible motion with readable text.
Lowering `amp` to fix collisions flattens the whole piece; lowering `k2` fixes
collisions while keeping the curve.

Sanity check for `k1`: cycles across the full width = `k1 * cols * spacingX / 2π`.
Keep it under about 2.

## Depth of field

Real defocus does most of the work in making a field look photographed rather
than drawn.

```js
const defocus = Math.abs(z - focusZ)
const blur    = Math.min(maxBlur, defocus * blurK)

const sharp     = 1 - Math.min(1, defocus / falloff)   // focus brightness
const depthFade = clamp01(1 - (z - fadeStart) / fadeRange)
const alpha     = (base + sharp * (1 - base)) * depthFade
```

Coupling **brightness to focus**, not just to depth, is what produces the
photographic look: the in-focus band reads bright and crisp while everything
else recedes. Position `focusZ` so the sharp band crosses the middle of the
composition, which is where the legible text should land.

See `motion.md` for blur quantisation and draw ordering.

## Composition

- **Vignette** the frame so corners do not compete with the focal band.
- **Keep colour out of it.** These fields are strongest monochrome. If you add
  an accent glow, keep it low, small, and *behind* the in-focus subject so it
  reads as light coming off the surface rather than a wash over the frame. A
  large tint at the top edge reads as haze and dirties the whites.
- **Let it bleed** off the frame edges. A field that stops inside the canvas
  looks like a diagram; one that runs off looks like a window.

## Determinism

Do not use unseeded randomness for anything you might need to reproduce. Either
use a seeded PRNG or drive variation from the point's own indices. You will want
to re-render at a different size, and "the good one" must be recoverable.

## Rendering to a file

For a static deliverable, render in headless Chrome:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --disable-gpu --hide-scrollbars \
  --virtual-time-budget=25000 \
  --force-device-scale-factor=2 \
  --window-size=1200,630 \
  --screenshot=out.png "file://$PWD/art.html"
```

- `--virtual-time-budget` must exceed font load plus draw time.
- `--force-device-scale-factor=2` gives a 2x render for retina output.
- Wait for fonts before drawing, or the first render uses a fallback:
  `document.fonts.load('500 24px Family').then(() => document.fonts.ready).then(draw)`

Keep every tunable in one config object at the top of the file. These pieces are
found by iteration, and the person iterating needs one place to turn the dials.
Comment which parameters trade against each other.
