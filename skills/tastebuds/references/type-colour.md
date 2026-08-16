# Type and colour

## Building the scale

Fewer sizes, used consistently, beats a size for every occasion. A tight scale
forces hierarchy to come from weight, colour, and space, which is where it
should come from anyway.

Start with seven or eight roles and resist adding more:

```
Display   80 / 64      hero only
Heading   60 / 48      section headings
Stat      28           feature numbers, pull figures
Body      18 / 16 / 14 paragraphs, cards, captions
Label     12           eyebrows, meta, legal
Button    14
```

Two families at most. One workhorse for everything, optionally one secondary
reserved for a single role (uppercase labels are the usual candidate). If you
cannot name the rule for when the second family appears, use one.

## Treatment by size

Tracking and line height are not constants, they are functions of size:

| Size | Tracking | Line height |
|---|---|---|
| 60px+ | `-0.02em` to `-0.025em` | `1.02` – `1.08` |
| 28–48px | `-0.01em` to `-0.02em` | `1.1` – `1.2` |
| Body | `0` to `-0.005em` | `1.4` – `1.45` |
| Uppercase labels | `+0.14em` to `+0.16em` | `1.2` |

Large type set at default tracking looks loose and amateurish. Uppercase set
without added tracking looks cramped. These two adjustments do more for
perceived quality than font choice.

## Measure and ragging

Body copy wants 45 to 75 characters per line. Constrain with `max-width` in
`ch` or a fixed px value tuned to the size, never by hoping the container is
the right width.

Check the rag on headings at every breakpoint. A heading that drops a single
short word onto its own line ("anything.", "market.") reads as broken. Fix with
a measure change, a non-breaking space before the last word, or a rewrite.

Hyphenated compounds break at the hyphen, which is correct typography but can
strand a fragment like "non-" at a line end. If it lands badly, a non-breaking
hyphen (`U+2011`) prevents the break. **Check the glyph after substituting**:
not every family includes `U+2011`, and a font fallback for that one character
is far more noticeable than the original break. If the family lacks it, rewrite
instead.

## Colour architecture

One ground, one text, two or three greys, one accent. That is the whole palette
for most work.

```
ground      #050505      near-black, not pure black
text        #FCFCFC      near-white, not pure white
secondary   #9A9A9A      body copy on dark, supporting text
tertiary    #6A6A6A      labels, meta, legal
accent      one colour   used once per view
```

Pure black and pure white are harsher than their near neighbours and make
antialiasing worse. Near-black also gives you somewhere to go darker.

Invert the same structure for light designs. The relationships matter, the
specific values do not.

## Spending the accent

An accent works by scarcity. The moment it covers area it becomes a background
and stops accenting anything.

Good uses: a single punctuation mark, one small state indicator, a thin rule,
a focused element in a field of unfocused ones, one glow at low opacity behind
a subject.

Bad uses: filling a hero, tinting a whole section, colouring every heading,
a gradient wash. If a coloured region reads as haze rather than as light, it is
too large or too strong. Shrink it and move it behind the subject rather than
over the whole frame.

## Light sections in a dark page

Alternating a dark page with occasional light sections creates rhythm and stops
a long page feeling like one endless surface. Use them sparingly and at
structural moments.

Two costs to plan for:

1. Any fixed header must work over both. See `layout-grids.md`.
2. Every token used in the section needs a light-mode counterpart. A grey tuned
   for a dark ground will be illegible on a light one.

## Contrast

Work it out rather than eyeballing it, especially for text over composited or
translucent surfaces.

- Body text: aim 7:1, accept 4.5:1.
- Large text (24px+ or 19px+ bold): 4.5:1 minimum, 3:1 absolute floor.
- Non-text UI (icons, borders that carry meaning): 3:1.

For text over a translucent layer, composite first:
`result = alpha * overlay + (1 - alpha) * background`, then measure against that.

Automated linters commonly evaluate text against the raw page background and
ignore an intervening backdrop, producing false positives. Verify visually
before acting on a contrast warning, and verify by maths before dismissing one.

## Decorative texture

Low-opacity repeating texture (dot grids, fine noise, subtle patterns) adds
depth cheaply. Keep it under about 6% opacity against the ground.

If the same texture appears in more than one place, define it once and reference
it everywhere. When two implementations of "the same" grid drift apart by a
pixel of pitch, the seam between them becomes visible even though neither is
individually noticeable.
