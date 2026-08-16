# Social and OG cards

## Format

- **1200 × 630** is the spec and it is what every major platform expects. It is
  exactly 1.91:1, so `summary_large_image` does not crop it.
- A 2400 × 1260 render is sharper on retina and safe to use. Most platforms
  re-encode anyway, so it is a small win.
- Keep meaningful content inside roughly 60px of the edges. Some surfaces crop
  to squarer ratios.

## The tags that matter

```html
<meta property="og:title"       content="...">
<meta property="og:description" content="...">
<meta property="og:image"       content="https://absolute.url/card.png">
<meta property="og:url"         content="https://...">
<meta property="og:type"        content="website">
<meta name="twitter:card"       content="summary_large_image">
```

**`twitter:card` is the one people forget.** Without it, X renders a small
square thumbnail and the card is wasted. Many site builders emit it
automatically when a social image is set. Verify rather than assume.

The image URL must be absolute. Relative paths fail everywhere.

Validate with each platform's own inspector before the link circulates.
**Caches are aggressive and long-lived**, so getting it right before first share
is much cheaper than fixing it after.

## Writing the title and description

**Do not repeat in the title what the image already says.** If the card art
carries the tagline, the title's one line should be spent explaining what the
thing actually is. A card that says the same sentence twice has wasted its only
chance to inform.

- **Title**: brand plus category, concrete. Around 50 to 60 characters.
- **Description**: what you can actually do, in plain words. Around 150 to 195
  characters; some feeds cut near 160, so front-load the substance.

Every claim in a social card is subject to the same discipline as page copy.
See `copy-voice.md`.

## The legibility trade

There are two viable card designs and they optimise for different places:

**Typographic.** Logo, one large headline, one supporting line. Readable at any
scale including a small preview. Safe, informative, easy to make boring.

**Art-led.** A generated or photographic image carrying the message through
form. Stops the scroll where cards render large. At feed size, roughly 500px
wide, fine detail becomes texture and small text stops being readable.

**Decide by where the link actually gets shared.** Large-card surfaces favour
art; chat-app previews and dense feeds favour type. If you cannot choose, put a
legible line in a corner of the art-led card so it degrades gracefully.

Test by scaling the card to 500px wide and looking at it. Whatever you can still
read is what the card communicates.

## Construction

Building the card as a standalone HTML file and rendering headless gives you
version control, exact pixel dimensions, and cheap re-rendering. See the
rendering recipe in `generative-canvas.md`.

Use the real design tokens and the real logo vector rather than approximations.
A social card that is subtly off-brand is worse than one that is plainly simple.

**Card art hard-codes copy.** If the tagline or product description changes, the
card does not follow. Note it wherever time-sensitive content is tracked, and
keep the generator source alongside the output so re-rendering is trivial.
