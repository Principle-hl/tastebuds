---
name: tastebuds
description: >
  Opinionated design sensibility and construction discipline for web interfaces:
  spacing rhythm, type scale, colour architecture, grid and reflow rules, motion
  performance patterns, generative canvas work, copy density and progressive
  disclosure, phones and in-app browsers, component authoring, copy voice, and a
  pre-launch QA pass. Use when designing, building, reviewing, or polishing any
  website, landing page, marketing site, app surface, component, or social/OG
  image, in any domain. Also use when a layout "looks off" but the cause is not
  obvious, when a page has "too much to read", when something renders wrong on a
  phone or in a Telegram/Instagram in-app browser, or before shipping anything
  visual.
---

# TasteBuds

A design sensibility, plus the construction rules that make it survive contact
with real breakpoints.

Most design guidance fails because it stops at principles. The gap between "use
consistent spacing" and a page that actually holds together is a specific set of
traps, and this skill is mostly about those. Read the principles once. Return to
the references when you are building or debugging.

## The six principles

**1. Alignment is the whole game.**
Almost everything that reads as "unprofessional" is a broken edge. Every element
in a section should hang off one left edge. Outer edges of a grid sit flush with
the heading above them. If something is 12px off, it is not a rounding detail,
it is the reason the page feels cheap. Before adding anything, check what is
already misaligned.

**2. Every effect must earn its place.**
Hover states that only change a background tint, animations that just move
something, gradients that only fill space: cut them. A hover state should
communicate affordance or reveal information. If you cannot say what an effect
tells the user, delete it. Restraint reads as confidence.

**3. One accent, used once.**
Pick a single accent colour and spend it in one small place per view. A single
accented full stop beats an accent-coloured wash. When an accent covers area it
stops being an accent and becomes a background, and the design goes muddy.

**4. Rhythm before detail.**
Uniform vertical spacing across every section does more for a page than any
individual section's polish. Standardise the rhythm first, then design inside
it. Deliberate exceptions are fine, but they must be deliberate and recorded.

**5. Depth through value, not ornament.**
Dark grounds, low-opacity textures, and blur carry atmosphere far better than
borders, shadows, and decoration. If a surface feels flat, reach for contrast
and depth cues before you reach for another element.

**6. Structure before rewriting.**
When there is too much to read, the cause is usually a layout that collapses
badly rather than prose that is too long. A card grid that scans well three
across becomes an unbroken run of paragraphs in one column. Measure where the
words actually are, fix the structure, and most of the problem disappears
without touching a sentence. See `references/progressive-disclosure.md`.

## Defaults to start from

These are opening positions, not laws. They are tuned for a content-led page
and are a reasonable starting point in most domains.

**Vertical rhythm.** Three steps, scaling roughly 1 : 0.75 : 0.5 across
desktop / tablet / phone. A concrete set that works: `150 / 112 / 76`.
Apply the same top and bottom to every section.

**Type scale.** Keep it tight. Every extra size is a decision you will get
wrong somewhere:

| Role | Desktop |
|---|---|
| Display | 80 / 64 |
| Heading | 60 / 48 |
| Stat or feature number | 28 |
| Body | 18 / 16 / 14 |
| Label, eyebrow | 12 |
| Button | 14 |

**Type treatment.** Negative tracking on large sizes (about `-0.02em` at 60px+),
normal at body size, and generous positive tracking on uppercase labels
(`0.14em` to `0.16em`). Line height about `1.05` for display, `1.4` for body.

**Colour architecture.** One ground, one text colour, two or three greys, one
accent. For a dark design: ground near `#050505`, text near `#FCFCFC`, secondary
text near `#9A9A9A`, tertiary near `#6A6A6A`. Pure `#000` and pure `#FFF` are
usually worse than near-black and near-white.

**Section anatomy.** Eyebrow (uppercase, tracked, tertiary colour), heading,
body paragraph at a constrained measure, then content. Consistency here is what
makes a long page feel authored rather than assembled.

## Non-negotiables

- **Never write copy containing em-dashes.** Use a full stop, a comma, or
  restructure. This is a house rule and it applies to all visible text.
- **Never print a number you have not verified.** Performance figures, market
  share, user counts, timings. If the source does not state it, do not claim it.
  Hedge or cut. See `references/copy-voice.md`.
- **Never let a fixed nav sit at low contrast over any section it passes.**
  See the trap in `references/layout-grids.md`. This one ships broken constantly.
- **Never drive per-frame animation through component state.** Write to the DOM
  or to a ref. See `references/motion.md`.
- **Never trust a write that you have not read back.** Edit layers accept and
  silently drop values. A success response is not evidence.
- **Never conclude from a property audit that something renders correctly.**
  Values prove what a node is, not what it looks like. Compositing faults leave
  every property correct. See `references/qa-pass.md`.

## Working order

1. **Establish rhythm and scale first.** Spacing, type scale, colour tokens.
   Everything after is cheaper.
2. **Build the section anatomy once**, then repeat it. Variation should come
   from content, not from restructuring.
3. **Check every breakpoint as you go**, not at the end. Reflow bugs are cheap
   to prevent and expensive to find. See `references/layout-grids.md`.
4. **Add motion last**, and only where it carries meaning.
5. **Run the pre-launch pass** in `references/qa-pass.md` before shipping.

## References

Load these when the work touches them, not upfront.

- **`references/layout-grids.md`**: grid construction, flush-edge rules, the
  reflow trap that breaks card grids at every breakpoint but the one you
  designed on, nav layout, and fixed-header contrast.
- **`references/type-colour.md`**: building a type scale, measure and ragging,
  contrast maths, and how to use an accent without ruining it.
- **`references/motion.md`**: performance patterns, render paths, reduced
  motion, scroll-linked ranges, and how to verify motion you cannot watch.
- **`references/generative-canvas.md`**: generative type fields, depth of field,
  perspective projection, and the legibility maths that keeps generative text
  readable instead of turning into soup.
- **`references/copy-voice.md`**: voice, claim discipline, and writing headings
  and labels that say something.
- **`references/qa-pass.md`**: the pre-launch audit, including the verification
  discipline that stops you "fixing" things that were never broken.
- **`references/social-cards.md`**: OG and social image construction, the tags
  that actually matter, and the legibility trade at feed size.
- **`references/progressive-disclosure.md`**: measuring copy density, the
  multi-column to one-column collapse, and when to collapse, delete or tabulate.
- **`references/mobile-webviews.md`**: in-app browsers, host chrome you cannot
  measure, fixed-element behaviour during scroll, safe areas, and floating
  controls that cross both light and dark grounds.
- **`references/components.md`**: authoring components inside a visual editor,
  including the silent compile failure, theme pairing, and cached instance data.
