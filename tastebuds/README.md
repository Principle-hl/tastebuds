# TasteBuds

An opinionated design sensibility for web interfaces, plus the construction
rules that make it survive contact with real breakpoints.

Packaged as an [Agent Skill](https://docs.claude.com/en/docs/claude-code/skills)
for Claude Code, but it is plain Markdown. It reads perfectly well as a document
if you just want the content.

## Why it exists

Most design guidance stops at principles. "Use consistent spacing" is true and
useless: the gap between knowing it and shipping a page that actually holds
together is a specific set of traps, and those are what this is mostly about.

Every rule here came from something breaking in production and being diagnosed,
not from a style guide. Where a rule has a cost or a trade-off, it says so.

## What is in it

`SKILL.md` carries the sensibility: six principles, concrete starting defaults
for spacing, type and colour, the non-negotiables, and a working order.

The references go deep and are meant to be opened when the work touches them:

| File | Covers |
|---|---|
| `layout-grids.md` | Flush-edge rules, the reflow trap that breaks card grids at every breakpoint but the one you designed on, distribution fragility, fixed-header contrast, stacking |
| `type-colour.md` | Type scale, tracking and line height by size, measure and ragging, contrast maths, spending an accent |
| `progressive-disclosure.md` | Measuring copy density, the multi-column to one-column collapse, when to collapse, delete or tabulate |
| `mobile-webviews.md` | In-app browsers, host chrome you cannot measure, fixed-element behaviour during scroll, safe areas, floating controls |
| `motion.md` | Performance patterns, the three render paths, scroll-linked ranges, reveal direction, verifying motion you cannot watch |
| `generative-canvas.md` | Point and type fields, depth of field, perspective, the legibility maths that keeps generative text readable |
| `components.md` | Authoring components inside a visual editor: silent compile failures, theme pairing, cached instance data |
| `copy-voice.md` | Voice, claim discipline, headings that say something |
| `qa-pass.md` | The pre-launch audit, and the verification discipline that stops you "fixing" things that were never broken |
| `social-cards.md` | OG image construction, the tags that actually matter, the legibility trade at feed size |

## A sample of the kind of thing it contains

**The reflow trap.** Card cell padding encodes a column count. A three-up grid
uses a repeating cycle of left and right paddings so the outer edges sit flush.
Reflow that grid to two columns and the flush cell lands in the right-hand
column; at one column you get an alternating stagger. It is invisible on the
breakpoint you designed on.

**Fixed headers over changing backgrounds.** A dark tint at low alpha over a
light section composites to a mid grey, and white labels on it land near 2:1.
Raising the tint alpha is nearly free, because a near-black tint over a
near-black section is invisible at any alpha, so it only bites where the header
was actually broken.

**Property audits and visual audits catch different bugs.** Reading a value back
proves what a node is, not what it looks like. A compositing fault leaves every
property correct, so the audit comes back clean while the render stays wrong.

## Install

Copy the folder into your skills directory.

```bash
# available in every project
git clone https://github.com/Principle-hl/tastebuds ~/.claude/skills/tastebuds

# or scoped to one project
git clone https://github.com/Principle-hl/tastebuds .claude/skills/tastebuds
```

Then invoke it with `/tastebuds`, or let it trigger on its own when a task
matches the description in the frontmatter.

## Scope

Deliberately domain-agnostic. Nothing here assumes a particular industry, brand
or product. The numbers are labelled as starting positions rather than laws, and
are meant to be argued with.

## Licence

MIT. Use it, change it, ship it, sell what you build with it. Keep the
copyright line and you are square.
