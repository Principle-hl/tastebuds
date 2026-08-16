# The pre-launch pass

## Verify before you fix

**The most expensive mistake in a review pass is fixing something that was never
broken.**

Review evidence lies in specific, predictable ways:

- **Static previews take a different code path.** Design-tool canvases, SSR
  snapshots, and screenshot renderers often render a component's static branch
  regardless of viewport, so a responsive component shows its wide layout at
  every width, complete with clipping and overflow that never occur live. Before
  filing a layout bug from a static capture, confirm which branch drew it.
- **Compression invents defects.** JPEG artefacts around text routinely read as
  font substitution, weight shifts, or spacing errors. Confirm against the
  source values before acting.
- **Linters miss composited layers.** Contrast checkers commonly measure text
  against the raw page background and ignore an intervening translucent
  backdrop, producing false failures. They also miss real failures where a
  fixed element passes over content they never associate with it.
- **Automated reviewers agree with each other confidently and are still wrong.**
  Multiple independent reports of the same issue raise the prior that it is
  real, but do not confirm it. Two reviewers looking at the same misleading
  screenshot produce the same wrong conclusion.

For each finding, name the evidence that would confirm it, then get that
evidence. Usually it is a property value, a computed style, or a render at the
actual width.

## Structural sweep

Walk the tree at every breakpoint and check for:

- Empty text nodes and placeholder strings that survived
- Interactive-looking elements with no action attached
- Links with missing, malformed, or placeholder targets
- Elements wider than their container
- Orphaned styles from deleted variants
- Anything hidden at one breakpoint that leaves a hole at another

Any element that both looks clickable and does nothing is either a bug or needs
its affordance removed.

## Geometry sweep

For each section, at each breakpoint:

- Section top and bottom **visible** gaps match the rhythm, or are a recorded
  exception. Measure ink to boundary, not the padding attribute.
- Every grid cell's text left edge aligns with the section heading. Misalignment
  here is the reflow trap in `layout-grids.md`.
- Nothing overflows the viewport horizontally.
- Headings do not strand a single short word on the last line.
- Fixed headers pass contrast over the lightest and darkest sections.
- Corner radii are consistent across cards, images, and media.

## Bulk edits need read-back

When applying many programmatic changes:

- **Apply, read back, retry.** A "success" response is not evidence the value
  changed. Verify by reading the property and comparing to what you intended.
- **Batch by scope, not by convenience.** Writes spanning multiple responsive
  variants in one transaction can bleed between them, leaving one variant
  holding another's value. One scope per transaction.
- **Silent-ignore is common.** Type mismatches (a quoted number where a number
  is expected), values invalid for the current layout mode, and properties that
  conflict with a sibling setting are frequently accepted and dropped without
  error. Read-back is the only reliable check.
- **Deleted nodes may still resolve.** Verify deletion by re-walking the parent,
  not by querying the removed id.

## Links and assets

- Every outbound URL returns 200. Check them, do not read them.
- Required attribution links for licensed components or data are present.
- Images have alt text; decorative ones are explicitly marked decorative.
- Every image referenced actually loads at every breakpoint. A variant hidden on
  one breakpoint can leave another pointing at nothing.

## Motion

- Static frame is correct (see `motion.md`).
- Reduced-motion path is correct.
- Scroll-linked animations complete before the page runs out of scroll.
- Nothing animates layout properties on scroll.

If motion cannot be observed in your environment, verify via preview controls at
start, middle, and end, and say plainly in your report that it was verified by
static frames rather than by watching it. Do not imply coverage you do not have.

## Reporting

Separate **confirmed** from **plausible**. For each confirmed issue give the
evidence. For each dismissed one give the reason, so nobody re-raises it.

Record dismissed-by-design decisions somewhere durable. An undocumented
deliberate choice is indistinguishable from a bug and will be "fixed" later.

Report honestly: if something was not checked, say so. A pass that claims
completeness it does not have is worse than no pass.

## Content with a clock

Before shipping, list every element that becomes false on a date: campaigns,
dated offers, "new" badges, year references, roadmap claims. Note the date and
decide now who changes it. This is the most common way a polished page goes
stale.

## Property audits and visual audits catch different bugs

Reading a value back proves what a node **is**. It says nothing about what it
**looks like**.

A compositing fault (an overlay dimming an element, a mask eating a background,
a blend mode cancelled by an ancestor) leaves every property correct. Audit the
attributes twice and it comes back clean twice, while the render stays wrong.

So run both passes, and when they disagree, believe the render. If a value is
verifiably correct and the result still looks wrong, stop rechecking the value
and start looking at what is painted over, under, or through it.

The reverse also holds: a screenshot cannot tell you whether a value is a
deliberate choice or an accident. Use property reads to distinguish those.

## Fixing one alignment makes the next one louder

Misalignment hides in company. When several elements are inconsistently inset,
none of them reads as wrong. Flush most of them and the remaining one becomes
obvious immediately.

Expect a second pass after any alignment fix, and expect a reviewer scrolling
the real thing to find what per-section screenshots did not. Looking at sections
in isolation is exactly the wrong frame for catching a broken edge, because the
defect is a relationship between sections.

## Real devices find a different class of bug

Reserve a pass on the actual hardware, in the app the link will actually be
opened in. It is the only way to catch host browser chrome, fixed-element
behaviour during scroll, touch target comfort, and how a floating layer sits
over real content. See `mobile-webviews.md`.

Static previews and design tool canvases cannot produce any of these.
