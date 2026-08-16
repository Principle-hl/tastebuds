# Density and progressive disclosure

The usual complaint is "there is too much to read". The usual response is to
start rewriting, which is slow and loses information. Measure first: the problem
is almost always structural and concentrated in a few places.

## Measure before you cut

Count **words** and **text blocks** per section, at phone width. Two things fall
out almost every time:

1. **The distribution is extremely lopsided.** A handful of sections hold most
   of the copy. Sorting by word count tells you where to spend effort and stops
   you trimming sections that were never the problem.
2. **The heavy sections share one shape.** They are usually the same repeated
   component, which means one structural fix repairs all of them at once.

A page can easily have three quarters of its copy in four sections. Do not
redesign fifteen sections when four are responsible.

## The multi-column to one-column collapse

The single most common cause. A card grid where each cell is eyebrow, heading
and a short paragraph reads well two or three across: the eye scans headings and
dips into whichever paragraph it wants.

Stack that same grid into one column and it becomes an unbroken run of
paragraphs. Nothing changed except column count, and the reading load went up
enormously. Twenty cells is twenty consecutive paragraphs.

**Symptom to listen for:** "it looks like poorly aligned paragraphs", or a
reader saying they lose their place scrolling.

## The heading is already the summary

Before writing new summary copy, check what is there. Cells like this usually
already carry a written one-line summary in the heading:

> "No cadence to read"
> "Exits start before the entry finishes"
> "Pause, not cancel"

Those are the scannable layer. The paragraph is the detail layer. So the fix is
**structural, not editorial**: keep eyebrow and heading visible, put the
paragraph behind a tap. Nothing is deleted, every feature stays on the page, and
what replaces the wall of text is a scannable list of specific claims.

This typically removes half the visible copy without a single rewrite.

## Collapse or delete

Both are valid. Choose per section:

- **Collapse** when the detail is genuinely wanted by some readers: feature
  explanations, mechanics, caveats.
- **Delete** when a headline number or label already answers the question and
  the detail exists elsewhere. A stat card reading "110%" with a label does not
  need a paragraph restating it, especially if a FAQ or terms link sits nearby.
- **Convert** when several items differ along the same axes. Three options
  compared on cost, location and behaviour are a small table, not three
  paragraphs. A table says it in a third of the words.

An accordion of very short rows is worse than the paragraphs it replaced. If the
body is under about twenty words, trimming beats collapsing: the tap costs more
than the reading.

## Tap, not hover

Phones have no hover, and phones are the case this exists to serve. Use a tap
target with a visible affordance. If a desktop hover reveal is wanted too, it is
an addition, not the mechanism.

## Reuse the disclosure you already have

If the page has an FAQ accordion, use the same control, the same glyph and the
same motion for these rows. Readers meet it once and know it everywhere. Two
disclosure patterns on one page is one too many.

## Per-breakpoint defaults, one component

The same component can be open by default where there is room and collapsed
where there is not. That is one component with a per-breakpoint default, not two
designs. Beware: in many design tools **component property values are not
per-breakpoint**, so the responsive switch has to live in CSS media queries
inside the component rather than in a property.

## Where the words also hide

- **Section intros.** A 40 to 50 word paragraph under every heading adds up fast
  across a dozen sections. Twenty words is usually enough.
- **Footnotes and disclaimers.** These read as legal text and belong in an FAQ
  or a linked terms page, not in the flow.
- **Duplicates.** The same fact stated in two sections is common when sections
  are written at different times. Search for repeated claims; consolidate into
  whichever section owns the topic, and carry any unique detail across with it.

## What you gain

Expect roughly a halving of visible phone copy from structure alone, before any
rewriting, with every feature still present and one tap away.
