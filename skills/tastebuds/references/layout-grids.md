# Layout and grids

## Flush outer edges

A card grid sitting under a heading must align with that heading. The heading is
at the section's content edge, so the grid's outer edges must be too.

This means cell padding is **asymmetric by position**, not uniform:

- First column: `padding-left: 0`
- Last column: `padding-right: 0`
- Inner edges: carry the gutter (e.g. `30px`)

Uniform padding on every cell insets the whole grid by one gutter and it will
read as a mistake next to the flush heading, because it is one.

The same rule applies to any repeated row: stat rows, logo rows, footer link
columns. Outer edges flush, inner edges gutter.

## The reflow trap

**This is the single most common grid bug and it is nearly invisible on the
breakpoint you designed on.**

Cell padding encodes a column count. A three-up grid uses a repeating cycle:

```
cell 0: left 0,  right g     (first column)
cell 1: left g,  right g     (middle column)
cell 2: left g,  right 0     (last column)
cell 3: left 0,  right g     (cycle repeats)
```

When that grid reflows to **two** columns, the cycle no longer matches the
layout. Cell 2 now starts a row but still carries `left: g`, so it is indented.
Cell 3 is now a right-hand column but carries `right: g` and `left: 0`, so it is
flush on the wrong side. At **one** column, every cell with `left: g` sits
inset while the others are flush, producing an alternating stagger.

**Rule: cell edge padding must be derived from the column count at each
breakpoint, never copied from the desktop layout.**

```
padding-left  = (index % cols === 0)        ? 0 : gutter
padding-right = (index % cols === cols - 1) ? 0 : gutter
```

At one column, both are 0.

**How to catch it:** view every grid at every breakpoint and check that the
left edge of each cell's text lines up with the section heading. Any cell that
does not is carrying padding from a different column count.

## Padding attribute is not the visible gap

The number on a section's padding property is rarely the gap a person sees.
Child grids, cards, and lists add their own internal padding on top.

If a section has `112px` bottom padding and its last child is a card grid whose
cells carry `38px` bottom padding, the visible gap is `150px`.

**Standardise the visible gap, not the attribute.** Measure from the last
element's ink to the section boundary. When auditing, walk the actual rendered
geometry rather than reading padding values, or you will "fix" sections that
were already correct and leave the broken ones alone.

Decorative hairlines and rules also count as ink. A 1px divider flanking an
eyebrow will shift a naive measurement by a pixel and send you chasing nothing.

## Deliberate exceptions

Some sections should not match the rhythm. A section that visually hands off to
the next one (a strip, a band, a continuation) wants a short bottom so the two
read as connected.

That is fine, but **record it**, in a comment or a project note. An undocumented
exception is indistinguishable from a bug, and the next person to audit the page
will "correct" it.

## Distribution fragility

A three-group header (logo / links / actions) built with `space-between` places
the middle group wherever the leftover space falls. Its position therefore
depends on the widths of the two groups flanking it.

This looks centred by coincidence when the flanks happen to be similar widths,
and drifts the moment you add or remove a nav item, translate a label, or change
a button.

**Prefer explicit intent:**

- To pin links beside the logo: give the links group `flex: 1` (or width `1fr`)
  with its children packed to the start. It absorbs the free space instead of
  floating in it, and the actions group stays pinned right. Adding or removing
  items now changes nothing about where the remaining ones sit.
- To genuinely centre a group: centre it against the container, not against its
  siblings.

Spacing note: some layout engines refuse a `gap` while a stack is set to
`space-between`, and may accept the value while silently ignoring it. If a gap
does not appear, set padding on the child instead and verify by reading the
value back.

## Fixed headers over changing backgrounds

**A translucent fixed header will fail contrast over some section of a
long page, and it is almost never checked.**

A header with a dark tint at low alpha over a light section composites to a
mid grey. White labels on that grey land near 2:1, which is unreadable.

Work out the composite before trusting it:

```
result = alpha * tint + (1 - alpha) * background
```

A near-black tint at `0.26` over a `#F0F0F0` section gives about `#B3B3B3`.
White text on that fails badly.

**The fix is usually to raise the tint alpha, and it is nearly free.** A
near-black tint over a near-black section is invisible at any alpha, so
increasing it changes nothing on dark sections and only bites where the header
was actually broken. Around `0.7` gives roughly `#474747` under white text, near
8:1.

**Always test the header over the lightest and darkest sections on the page**,
not just at the top of the scroll where it is usually transparent anyway.

## Stacked layouts at intermediate widths

A two-column layout with a fixed-width left column and a large gap will crush
the right column at tablet before it ever reaches the phone layout that fixes it.

Check the arithmetic:
`available - fixed_column - gap = remaining`.

If `remaining` drops below about 45 characters of measure, stack the layout at
that breakpoint instead. A tablet layout that simply matches the phone is
usually better than a squeezed desktop layout.

When stacking, a heading block may keep a constrained width even though the
content below goes full width. That reads as a deliberate measure, not a bug.

## Corner radius

Pick one radius and use it everywhere: cards, images, media, insets. Mixed radii
across a page is one of those things nobody consciously notices and everybody
feels. When placing an image inside a rounded container, match the container.

## New nodes carry no stacking level

A layer you add to an existing composition starts with `z-index: auto`. If some
of its neighbours already carry explicit z-indices, being later in document
order is **not** enough to paint above them.

The failure is quiet and easy to misread. A scrim over hero media at
`rgba(5,5,5,0.55)` sitting above a new logo does not hide it, it **dims** it. A
near-white mark composites to roughly `#767676`, so the element looks like it
was set at half opacity, or like the wrong colour was applied.

That is why an attribute audit comes back clean. The colour property really is
correct. The fault is in compositing, not in the value.

**When something added to an existing section looks dimmed or tinted rather than
missing, check stacking before you check colour.** List every sibling's
z-index and give the new node one above the highest, rather than relying on
document order.

More generally: mixing explicit z-indices with `auto` in one container makes
paint order unpredictable to read. Either set them on every positioned child in
the container, or on none.
