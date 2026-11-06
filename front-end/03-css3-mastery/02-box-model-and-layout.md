# The Box Model, Display & Positioning

Every element generates one or more boxes. Understanding how those boxes are sized, stacked, and positioned removes most "mystery" from CSS layout.

---

## 1. The Four Layers of a Box

```
┌─────────────────────────────────────────────┐
│                  margin                     │
│   ┌─────────────────────────────────────┐   │
│   │              border                 │   │
│   │   ┌─────────────────────────────┐   │   │
│   │   │          padding            │   │   │
│   │   │   ┌─────────────────────┐   │   │   │
│   │   │   │      content        │   │   │   │
│   │   │   └─────────────────────┘   │   │   │
│   │   └─────────────────────────────┘   │   │
│   └─────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

```css
.card {
  width: 300px;
  padding: 24px;
  border: 2px solid #ddd;
  margin: 16px;
}
```

With the default `box-sizing: content-box`, the **rendered width is 300 + 48 + 4 = 352px**. This is the source of countless layout bugs.

### Fix it globally

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

Now `width: 300px` includes padding and border. The rendered box is exactly 300px wide.

> **Best practice:** Put `box-sizing: border-box` in your reset. There is almost no modern use case for `content-box`.

---

## 2. Margin Collapsing

Vertical margins between *adjacent* block-level siblings collapse into the larger margin — they do not add.

```css
h1 { margin-bottom: 24px; }
p  { margin-top: 16px; }
/* Gap between them is 24px, not 40px */
```

Margin collapsing does **not** happen:

- In flex or grid containers.
- Between a parent and child when the parent has padding, a border, or `overflow` other than `visible`.
- For absolutely positioned or floated elements.
- For horizontal margins.

> **Gotcha:** A child's top margin can "escape" its parent, pushing the whole parent down. Fix it with `padding-top` on the parent, `display: flow-root`, or a flex/grid context.

```css
.parent {
  display: flow-root; /* establishes a block formatting context, contains margins */
}
```

---

## 3. `display`: The Formatting Context Switch

| Value | Behavior |
| :--- | :--- |
| `block` | Full width, new line, honors width/height. |
| `inline` | Flows in text, ignores width/height and vertical margins. |
| `inline-block` | Flows inline but honors width/height. |
| `flex` | Children become flex items along one axis. |
| `inline-flex` | Flex container that flows inline. |
| `grid` | Children become grid items in rows and columns. |
| `inline-grid` | Grid container that flows inline. |
| `none` | Removed from layout and accessibility tree. |
| `contents` | Box disappears; children participate in the parent's layout. |
| `flow-root` | Block container that contains floats and margins. |

```css
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0 0 0 0);
  white-space: nowrap;
  border: 0;
}
```

> **Accessibility:** Use the class above to hide text visually while keeping it available to screen readers. Never use `display: none` for content that assistive technology needs.

---

## 4. Positioning

```css
.positioned {
  position: relative; /* stays in flow; establishes containing block */
  top: 4px;
  left: 8px;
}

.absolute {
  position: absolute; /* removed from flow; positioned against nearest positioned ancestor */
  inset: 0;
}

.fixed {
  position: fixed; /* positioned against the viewport (or transformed ancestor) */
  bottom: 1rem;
  right: 1rem;
}

.sticky {
  position: sticky; /* relative until a scroll threshold, then fixed within parent */
  top: 0;
}
```

### The containing block rule

For `position: absolute`, the containing block is the nearest ancestor with a `position` other than `static`, or a transform/filter/contain property. If none exists, it is the **initial containing block** (the viewport-sized box at the document root).

```css
.card {
  position: relative; /* now .card__badge anchors to the card */
}

.card__badge {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
}
```

> **Gotcha:** `position: fixed` behaves like `absolute` inside an ancestor with `transform`, `filter`, `perspective`, or `contain: paint`. This breaks many modal and sticky-header implementations.

### Centering an absolutely positioned element

```css
.modal {
  position: fixed;
  inset: 0;
  display: grid;
  place-items: center;
}
```

---

## 5. Stacking Contexts and `z-index`

`z-index` only works on positioned elements (or flex/grid items). It compares elements **within the same stacking context**.

A new stacking context is created by:

- `position` other than `static` **with** a `z-index` value.
- `opacity` less than `1`.
- `transform`, `filter`, `perspective`, `clip-path`, `mask`.
- `isolation: isolate`.
- `contain: layout` or `paint`.
- `will-change` on certain properties.
- Flex/grid children with `z-index` other than `auto`.

```css
/* Common layering scale */
:root {
  --z-base: 0;
  --z-dropdown: 100;
  --z-sticky: 200;
  --z-modal: 300;
  --z-toast: 400;
}
```

> **Gotcha:** A child with `z-index: 9999` cannot escape its parent's stacking context. If a modal appears behind a header, the problem is usually a transformed ancestor, not a too-small `z-index`.

---

## 6. Overflow and Scroll Containers

```css
.scroll-area {
  max-height: 20rem;
  overflow-y: auto;
  overscroll-behavior: contain; /* prevent scroll chaining to the page */
  scrollbar-gutter: stable;      /* avoid layout shift when the scrollbar appears */
}
```

- `overflow: hidden` clips content **and** creates a block formatting context.
- `overflow: clip` clips without creating a scroll container (better for decorative clipping).
- `overflow: auto` only shows scrollbars when needed.

```css
/* Horizontal card scroller without a visible scrollbar */
.carousel {
  display: flex;
  gap: 1rem;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  scrollbar-width: none;
}

.carousel > * {
  flex: 0 0 18rem;
  scroll-snap-align: start;
}
```

---

## 7. Aspect Ratio and Intrinsic Sizing

```css
.thumbnail {
  aspect-ratio: 16 / 9;
  width: 100%;
  object-fit: cover;
}

.square-avatar {
  inline-size: 3rem;
  aspect-ratio: 1;
  border-radius: 50%;
  object-fit: cover;
}
```

Use `min()`, `max()`, and `clamp()` for intrinsic, content-aware sizing:

```css
.container {
  width: min(100% - 2rem, 70rem);
  margin-inline: auto;
}
```

---

## 8. Logical Properties

Logical properties adapt to writing direction (`ltr`/`rtl`) and writing mode.

| Physical | Logical |
| :--- | :--- |
| `margin-left` | `margin-inline-start` |
| `margin-right` | `margin-inline-end` |
| `margin-top` | `margin-block-start` |
| `padding-bottom` | `padding-block-end` |
| `width` | `inline-size` |
| `height` | `block-size` |
| `top`/`bottom` | `inset-block-start`/`inset-block-end` |

```css
.card {
  padding-inline: 1.5rem;
  padding-block: 1rem;
  border-inline-start: 4px solid var(--brand);
}
```

---

## 9. Common Layout Recipes

### Sticky footer

```css
body {
  min-height: 100dvh;
  display: flex;
  flex-direction: column;
}

main { flex: 1; }
```

### Full-bleed section inside a centered container

```css
.container { width: min(100% - 2rem, 70rem); margin-inline: auto; }

.full-bleed {
  width: 100vw;
  margin-inline: calc(50% - 50vw);
}
```

> **Gotcha:** `100vw` includes the vertical scrollbar width on desktop, which can cause horizontal overflow. Prefer `100%` where possible.

### Media object

```css
.media {
  display: flex;
  gap: 1rem;
  align-items: flex-start;
}

.media__figure { flex: 0 0 auto; }
.media__body   { flex: 1; min-width: 0; }
```

`min-width: 0` allows the text block to shrink below its content's intrinsic width — essential for truncation.

---

## ✅ Checkpoint

- [ ] What is the rendered width of `width: 200px; padding: 20px; border: 5px` with `content-box`?
- [ ] Why does a child's top margin sometimes move the parent?
- [ ] When does `position: fixed` stop being viewport-relative?
- [ ] Why can a `z-index: 9999` element still render behind another element?
- [ ] What does `min-width: 0` fix in a flex layout?
