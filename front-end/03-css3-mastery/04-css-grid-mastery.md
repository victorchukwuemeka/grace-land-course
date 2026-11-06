# CSS Grid Mastery

CSS Grid is a **two-dimensional** layout system. You define tracks (rows and columns), then place items into cells or areas. It is the right tool for page-level layout, dashboards, galleries, and any design where rows and columns must align.

---

## 1. Basic Grid

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  grid-template-rows: auto 1fr auto;
  gap: 1rem 1.5rem; /* row-gap column-gap */
}
```

```
┌─────────┬─────────────────┬─────────┐
│  1fr    │       2fr       │  1fr    │
├─────────┼─────────────────┼─────────┤
│  auto   │                 │         │
├─────────┼─────────────────┼─────────┤
│  1fr    │                 │         │
└─────────┴─────────────────┴─────────┘
```

`fr` means "fraction of the remaining space **after** fixed tracks and gaps are subtracted."

---

## 2. The `repeat()` Function

```css
/* Instead of: 1fr 1fr 1fr 1fr */
.grid {
  grid-template-columns: repeat(4, 1fr);
}

/* Repeating pattern */
.grid {
  grid-template-columns: repeat(3, 1fr 2fr);
  /* same as: 1fr 2fr 1fr 2fr 1fr 2fr */
}
```

---

## 3. The Responsive Grid One-Liner

```css
.auto-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(18rem, 100%), 1fr));
  gap: 1.5rem;
}
```

- `auto-fit` creates as many tracks as fit, then **collapses empty tracks** so items stretch.
- `auto-fill` keeps empty tracks, leaving gaps.
- `minmax(min(18rem, 100%), 1fr)` prevents overflow on screens narrower than 18rem.

> **Gotcha:** `repeat(auto-fit, minmax(18rem, 1fr))` overflows on a 320px screen because 18rem (288px) plus padding may exceed the viewport. The nested `min()` fixes it.

---

## 4. Placing Items

```css
.layout {
  display: grid;
  grid-template-columns: 240px 1fr;
  grid-template-rows: 64px 1fr 48px;
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
  min-height: 100dvh;
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; min-width: 0; }
.footer  { grid-area: footer; }
```

### Line-based placement

```css
.feature {
  grid-column: 1 / 3;   /* start at column line 1, end at line 3 */
  grid-row: 2 / span 1; /* start at row 2, span one track */
}

/* Shorthand */
.feature {
  grid-area: 2 / 1 / 3 / 3; /* row-start / column-start / row-end / column-end */
}
```

Grid lines are numbered from **1**, and `-1` refers to the last line:

```css
.full-bleed {
  grid-column: 1 / -1; /* span every column */
}
```

---

## 5. Implicit Tracks

Items placed outside the explicit grid create implicit rows. Control their size with:

```css
.grid {
  grid-auto-rows: minmax(12rem, auto);
  grid-auto-flow: row;      /* row | column | dense */
  grid-auto-columns: 1fr;
}
```

`grid-auto-flow: dense` fills gaps with later, smaller items. Use sparingly — it can reorder content visually and hurt accessibility.

---

## 6. Alignment

```css
.grid {
  justify-items: start;   /* inline-axis alignment of items within cells */
  align-items: stretch;   /* block-axis alignment of items within cells */
  justify-content: space-between; /* distribute the whole grid */
  align-content: center;
  place-items: center;    /* shorthand for align-items + justify-items */
}
```

Per-item overrides:

```css
.grid__item {
  justify-self: end;
  align-self: start;
}
```

---

## 7. `minmax()`, `auto`, and Intrinsic Sizing

```css
.dashboard {
  grid-template-columns:
    minmax(12rem, 18rem)  /* sidebar: flexible but bounded */
    minmax(0, 1fr)        /* main: can shrink below content size */
    20rem;                /* fixed panel */
}
```

> **Gotcha:** `1fr` has an automatic minimum of `auto`, which can prevent shrinking. Use `minmax(0, 1fr)` when a grid child contains long content, tables, or `pre` blocks.

### `fit-content()`

```css
grid-template-columns: fit-content(20rem) 1fr;
```

The first track sizes to its content but never exceeds `20rem`.

---

## 8. Grid Areas for Page Shells

```css
.page {
  display: grid;
  grid-template-areas:
    "header"
    "main"
    "footer";
  grid-template-rows: auto 1fr auto;
  min-height: 100dvh;
}

@media (min-width: 60rem) {
  .page {
    grid-template-columns: 16rem 1fr;
    grid-template-areas:
      "header header"
      "sidebar main"
      "footer footer";
  }
}
```

This is often clearer than media-query-specific flex directions, because the layout is declared in one place.

---

## 9. Overlapping Items

Grid items can occupy the same cells. This replaces many absolute-positioning hacks.

```css
.hero {
  display: grid;
}

.hero > * {
  grid-area: 1 / 1; /* every child stacks in the same cell */
}

.hero__image { z-index: 0; }
.hero__content { z-index: 1; align-self: end; padding: 2rem; }
```

---

## 10. Subgrid

Subgrid lets a child grid inherit the parent's tracks, so nested items align with the outer layout.

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.card {
  display: grid;
  grid-row: span 2;         /* card spans two parent rows */
  grid-template-rows: subgrid;
}

.card__title { grid-row: 1; }
.card__body  { grid-row: 2; }
```

```css
/* Subgrid on one axis only */
.layout {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
}

.section {
  display: grid;
  grid-column: span 2;
  grid-template-columns: subgrid;
}
```

> **Browser support:** Subgrid is supported in all modern evergreen browsers. Provide a non-subgrid fallback for legacy targets if required.

---

## 11. Common Patterns

### Holy grail layout

```css
.holy-grail {
  display: grid;
  grid-template:
    "header header header" auto
    "nav    main   aside" 1fr
    "footer footer footer" auto
    / 12rem 1fr 14rem;
  min-height: 100dvh;
}
```

### Card gallery with consistent rows

```css
.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(16rem, 1fr));
  grid-auto-rows: 1fr;
  gap: 1rem;
}
```

### Sticky sidebar

```css
.sidebar {
  position: sticky;
  top: 1rem;
  align-self: start; /* prevents stretching to the full row height */
}
```

> **Gotcha:** A grid item stretches by default. If `position: sticky` seems broken, add `align-self: start`.

### Centering both axes

```css
.center {
  display: grid;
  place-items: center;
  min-height: 100dvh;
}
```

---

## 12. Debugging Grid

1. In DevTools, toggle the Grid badge on the container to see line numbers and area names.
2. Hover a grid item to highlight its placement.
3. Check the **Computed** tab for `grid-template-columns` — the browser shows the resolved track sizes.
4. If content overflows, look for `1fr` that should be `minmax(0, 1fr)`.
5. If items land in unexpected cells, check implicit placement order and `grid-auto-flow`.

---

## ✅ Checkpoint

- [ ] What is the difference between `auto-fit` and `auto-fill`?
- [ ] Why does `minmax(min(18rem, 100%), 1fr)` prevent mobile overflow?
- [ ] How do you make an item span the full grid width?
- [ ] Why might `position: sticky` fail on a grid item?
- [ ] What problem does subgrid solve?
