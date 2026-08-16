# Authoring components in a design tool

Notes for building code components that live inside a visual editor (Framer,
Webflow, Builder, and similar). These are the failures that cost the most time
because they are silent.

## Silent compile failure

A module-level template literal is evaluated at load. If it references constants
declared **below** it, that is a temporal dead zone error:

```js
const CSS = `.row { color: ${INK}; }`   // throws: INK not initialised
const INK = "#F2F2F2"
```

Many hosts surface this only as an **empty exports list**, never an error. The
component simply disappears from the canvas after an edit that looked fine.

**If a component vanishes after an edit, check declaration order first.** Hoist
every token above anything that interpolates it, and leave a comment saying why,
because the correct order looks arbitrary otherwise.

## Property values are often not per-breakpoint

Layout properties usually are. **Component property values usually are not**: one
instance carries one set of values across every breakpoint.

So a responsive component cannot take a `size` or `layout` property and have it
differ per breakpoint. The switch has to live in CSS media queries inside the
component. Design for that from the start rather than discovering it after
wiring up a property.

## Light and dark sections need paired tokens

A component built against a dark ground and dropped into a light section will
have near-white headings on near-white background. They do not look wrong in
code, they are simply invisible.

Give the component an explicit theme property with a full set of paired tokens,
scoped so both can exist on one page:

```css
.scope[data-theme="light"] .head { color: var(--ink-light); }
```

Remember the accent too. A green that reads on black is often too weak on white
and needs a darker sibling.

**Always render a new component into every section type it will be used in
before calling it done.**

## Instances cache complex property values

A property holding an array of objects (a list of rows, questions, items) is
usually **snapshotted into the instance** when it is created. Editing the
component's default list afterwards changes nothing on existing instances.

Symptoms: the code clearly has eight items, the canvas clearly shows seven.

The fix is to delete and recreate the instance, then reapply its other
properties. Know this before you promise a quick content edit. It is also a good
argument for keeping such content in the component and treating instances as
disposable.

## Writes can report success and change nothing

Any programmatic edit layer will accept some values and silently drop them:
wrong type (a quoted number where a number is expected), a value invalid for the
current layout mode, a property that conflicts with a sibling setting, or simply
a misremembered property name.

**Read the value back after writing it.** A success response is not evidence.
Where possible, apply, read, and retry mismatches in a loop.

Property names are worth checking rather than guessing: hosts often diverge from
CSS naming, and a wrong name behaves exactly like a rejected value.

## Newly created nodes carry no stacking level

See `layout-grids.md`. A node you insert has no z-index while its neighbours may
have explicit ones, so it can land underneath an overlay even though it is last
in document order.

## Give every animated component a preview property

See `motion.md`. Non-negotiable in a visual editor, because the canvas does not
run effects and you cannot otherwise see any frame but the first.

## Keep tunables in one block

Generative or heavily parameterised components are found by iteration. Put every
number in one config object at the top with comments on which ones trade against
each other. The person tuning it needs one place to turn dials, and future you
needs to know which dial costs legibility.
