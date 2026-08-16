# Motion

## When motion is justified

Motion should do one of: reveal structure, show causality, or carry the eye
between states. Motion that only decorates is noise, and it costs frame budget,
battery, and accessibility.

If you cannot say in a sentence what a piece of motion tells the user, cut it.
This applies especially to hover states, which accumulate quickly and often
communicate nothing beyond "something happened".

## The performance rule

**Never drive per-frame values through component state.**

A state update per frame re-renders the component tree sixty times a second and
will drop frames on anything but a fast machine.

Write per-frame values straight to the DOM through a ref:

```js
// per frame
el.current.style.transform = `translate3d(0, ${y}px, 0)`
el.current.style.clipPath  = `inset(0 ${pct}% 0 0)`
```

Use state only for values that change at interaction frequency, not frame
frequency. The same applies to canvas: compute into typed arrays and draw,
rather than mapping over objects and allocating each frame.

Prefer `transform` and `opacity`. Animating layout properties (width, height,
top, left, margin) forces layout on every frame and is the usual cause of
scroll jank.

## Three render paths

Any animated component has to handle three situations, and they must be checked
in this order:

1. **Static render**: design tool canvas, screenshot, SSR, print. No effects
   run. Must draw a sensible settled or representative frame.
2. **Reduced motion**: `prefers-reduced-motion`. Draw the settled frame, or a
   simplified layout with no animation.
3. **Live**: the full thing.

**Check static before reduced motion.** A static renderer often reports reduced
motion too, and if you check reduced first you never reach the static branch and
the canvas draws the wrong thing.

This ordering is also why static previews can mislead you during review. A
design-tool canvas takes the static branch **regardless of viewport width**, so
a component that switches layout at narrow widths will show its wide layout in
every canvas screenshot, complete with clipping that never happens live. Before
filing a bug from a static screenshot, check which branch produced it.

## A preview control is not optional

Add a numeric `previewAt` (0 to 1) property that drives the component to an
arbitrary point in its timeline.

You cannot review motion in a screenshot, and you often cannot review it in the
build environment at all. A preview control lets you verify the start, middle,
and end frames as still images, which catches the majority of motion bugs
without ever watching it run. It also gives the static render something correct
to draw.

Verify at 0, 0.5, and 1 at minimum.

## Scroll-linked ranges

Choose the scroll range against what is actually scrollable.

The common failure: an element near the bottom of the page linked to a range
that completes only when its bottom edge meets the viewport bottom. The page
runs out of scroll before that happens, the animation freezes part way, and the
final state is never seen.

For anything in the last screen of a page, finish the range against the middle
of the viewport rather than its bottom, so the animation completes well before
the document ends.

## Reveal direction

A clip and a translate are not interchangeable.

Translating a clipped element downward always uncovers its **bottom** edge
first. If you want a top-first reveal, animate the clip itself:

```js
// opens from the top edge downward
el.style.clipPath = `inset(0 0 ${remaining}% 0)`
```

Combine a downward drift with a clip that opens downward for a reveal that
reads as the content arriving rather than sliding in from off-screen. Upward
translation reads as "sliding up" and usually looks cheaper.

## Depth-of-field and blur cost

`filter: blur()` per draw call is expensive. When many elements need different
blur amounts:

- **Quantise** blur into steps (0.5px is fine visually).
- **Sort by depth** so the blur value changes monotonically, then set the filter
  only when the bucket changes. Thousands of draws collapse to a dozen filter
  switches.

Draw far-to-near (painter's algorithm) so near elements correctly overlap far
ones.

## Shared coordinate spaces

When one continuous effect spans several sections, give every instance the same
world-space parameters and offset each by its position in that world. Instances
tuned independently will not line up, and the seams show as soon as two are
visible at once.

Record the shared contract somewhere. It is not inferable from any single
instance.

## Blend modes

`mix-blend-mode` is cancelled by any ancestor that creates a stacking context:
`transform`, `filter`, `opacity` below 1, `will-change`, and others. It is
common for an effect to render correctly in a design canvas and be invisible in
the live page for exactly this reason.

If an effect depends on blending, verify it in the real page, not the canvas.
Plain alpha compositing is less fragile and usually good enough.
