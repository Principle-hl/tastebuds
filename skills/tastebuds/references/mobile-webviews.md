# Phones and in-app browsers

Most links shared socially open in an **in-app browser**, not the OS browser.
Telegram, Instagram, X, Slack and LinkedIn all embed a web view and draw their
own chrome over it. That environment breaks assumptions a desktop layout never
tests, and it is usually where a site is seen first.

## You cannot negotiate with host chrome

An in-app browser paints its own URL pill, back control and toolbars **over your
page**. You cannot move it, and you cannot measure it.

Critically, **`env(safe-area-inset-*)` does not account for it.** Those insets
describe the device (notch, home indicator), not an overlay the host app chose
to draw inside the web view. Adding top padding via `env()` is good hygiene for
notches and does nothing for this.

**So the fix is not to reclaim the top. It is to stop putting anything important
there.** On phone:

- Do not rely on a fixed top bar.
- Let the header scroll away, or move persistent controls to the bottom.
- A bottom-anchored control cluster avoids the busiest chrome, sits in the
  thumb zone, and is a familiar mobile pattern in its own right.

Offset a bottom cluster with `calc(<gap> + env(safe-area-inset-bottom))` so it
clears the home indicator and any bottom toolbar.

## Fixed elements jitter during scroll

iOS WKWebView does not reposition `position: fixed` continuously during momentum
scrolling or rubber-banding. It snaps them at the end of the gesture.

The symptom is a screenshot where a fixed bar appears detached, with page
content above it, or a control apparently duplicated. **This is a rendering
artifact, not a layout bug.** It settles when scrolling stops.

You cannot fix it in CSS. You can only reduce exposure by having fewer
fixed elements, especially at the top edge where the displacement is most
visible against incoming content.

Before debugging a mid-scroll screenshot, ask whether the page was moving.

## A full-width fixed bar blocks taps

Centring a floating cluster usually means giving its container full width. That
container then lays an invisible strip across the screen that swallows taps on
whatever is behind it.

```css
.cluster        { pointer-events: none; }
.cluster > *    { pointer-events: auto; }
```

Only the controls should catch input. This is easy to miss because nothing looks
wrong, the page simply stops responding in a band you cannot see.

## Floating controls cross both grounds

A control pinned over a scrolling page will pass over every background the page
has. On an alternating light and dark page, one fill cannot serve both.

- **White fill with dark glyphs** works on any ground, and is the simplest
  choice for a floating cluster.
- A dark fill needs a light border, or it disappears against dark sections.
- A **heavily blurred, low-alpha drop shadow** separates a white control from a
  light background and stays invisible against dark ones. Around
  `0 4px 24px rgba(0,0,0,0.22)` is a good starting point: enough to lift the
  shape on white, not enough to read as a shadow anywhere else.

## Content must clear the floating layer

Anything fixed floats over the page, so the last element of a section and the
footer will sit underneath it. Add bottom padding equal to the cluster height
plus its offset, or the final line of the page is permanently half covered.

This is easy to forget because it only shows at the very end of a scroll, which
is exactly where a quick check stops.

## Viewport units

`vh` measures the viewport as if browser chrome were hidden, so a full-height
hero pushes its own CTA under the URL bar on a phone. `svh` is the height with
chrome showing, which is what "full height" usually means. `dvh` resizes mid
scroll, which causes reflow.

Prefer `min-height: 100svh` with `100vh` as a fallback above it, and let height
stay `auto` so short landscape screens grow rather than clip.

## Test where it will actually be opened

If the link will mostly be shared in a messaging app, open it there before
launch. The OS browser is the easy case and it is not the one most people see.
