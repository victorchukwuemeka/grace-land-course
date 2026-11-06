# Flexbox Mastery

Flexbox is a **one-dimensional** layout system: it distributes space and aligns items along a single main axis (row or column), with a cross axis perpendicular to it.

Use Flexbox when content size should drive layout. Use Grid when the layout should drive content size.

---

## 1. The Two Axes

```css
.row {
  display: flex;
  flex-direction: row;        /* main axis: horizontal */
  justify-content: center;    /* main-axis alignment */
  align-items: center;        /* cross-axis alignment */
}
```

```
flex-direction: row
┌──────────────────────────────────────────────┐
│  main axis ────────────────────────────────▶ │
│  ┌────────┐  ┌────────┐  ┌────────┐          │
│  │ item 1 │  │ item 2 │  │ item 3 │          │
│  └────────┘  └────────┘  └────────┘          │
│  cross axis ▼                                │
└──────────────────────────────────────────────┘
```

`flex-direction: column` swaps the axes: main becomes vertical, cross becomes horizontal.

> **Mental model:** `justify-*` always follows the **main** axis. `align-*` always follows the **cross** axis.

---

## 2. Container Properties

```css
.flex-container {
  display: flex;
  flex-direction: row;        /* row | row-reverse | column | column-reverse */
  flex-wrap: wrap;            /* nowrap | wrap | wrap-reverse */
  justify-content: space-between;
  align-items: stretch;
  align-content: flex-start;  /* only matters when wrapping */
  gap: 1rem;                  /* row-gap column-gap */
}
```

| Property | Values | Effect |
| :--- | :--- | :--- |
| `justify-content` | `flex-start`, `center`, `flex-end`, `space-between`, `space-around`, `space-evenly` | Main-axis distribution |
| `align-items` | `stretch`, `flex-start`, `center`, `flex-end`, `baseline` | Cross-axis alignment of items |
| `align-content` | same as above | Cross-axis distribution of wrapped lines |
| `gap` | length | Space between items (not outside edges) |

> **Gotcha:** `align-content` does nothing on a single-line flex container. You need `flex-wrap: wrap` and multiple lines.

---

## 3. Item Properties

```css
.flex-item {
  flex-grow: 1;      /* how much extra space to absorb */
  flex-shrink: 1;    /* how much to shrink when space is tight */
  flex-basis: 20rem; /* initial main-axis size */
  align-self: center;/* override align-items for this item */
  order: 2;          /* visual order — not reading order */
}
```

### The `flex` shorthand

```css
.item-a { flex: 0 1 auto; } /* default */
.item-b { flex: 1; }        /* flex: 1 1 0% — fills remaining space equally */
.item-c { flex: 0 0 200px; }/* fixed size, no grow/shrink */
.item-d { flex: 0 0 auto; } /* size to content */
```

| Shorthand | Equivalent | Typical use |
| :--- | :--- | :--- |
| `flex: 1` | `1 1 0%` | Equal-width columns |
| `flex: auto` | `1 1 auto` | Grow from content size |
| `flex: initial` | `0 1 auto` | Default behavior |
| `flex: none` | `0 0 auto` | Fixed, content-sized item |

> **Gotcha:** `flex: 1` uses `flex-basis: 0%`, so items become equal width regardless of content. `flex: auto` uses the content size as the starting point, so items can end up unequal.

---

## 4. Sizing and the `min-width: 0` Fix

Flex items default to `min-width: auto` (or `min-height: auto` in columns), meaning they refuse to shrink below their content's minimum size. Long words, `<pre>` blocks, and tables frequently overflow because of this.

```css
.flex-item {
  flex: 1;
  min-width: 0; /* allow shrinking below content size */
}

.truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

For column layouts, use `min-height: 0` to allow a scrollable child to shrink.

```css
.app {
  display: flex;
  flex-direction: column;
  height: 100dvh;
}

.app__main {
  flex: 1;
  min-height: 0; /* critical for nested scroll areas */
  overflow-y: auto;
}
```

---

## 5. Alignment Cheat Sheet

```css
/* Perfect centering */
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
/* Or: display: grid; place-items: center; */
```

```css
/* Navbar: logo left, links right */
.navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 2rem;
}

.navbar__links {
  display: flex;
  gap: 1.5rem;
  margin: 0;
  padding: 0;
  list-style: none;
}
```

```css
/* Card with footer pinned to the bottom */
.card {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.card__body { flex: 1; }
```

```css
/* Input with a button attached */
.search {
  display: flex;
}

.search__input {
  flex: 1;
  min-width: 0;
  border-radius: 0.5rem 0 0 0.5rem;
}

.search__button {
  flex: none;
  border-radius: 0 0.5rem 0.5rem 0;
}
```

---

## 6. Wrapping and Responsive Cards

```css
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
}

.card-grid > * {
  flex: 1 1 18rem; /* grow, shrink, ideal width */
  max-width: 100%;
}
```

This is the classic "flex wrap" responsive grid: cards sit at a minimum of ~18rem and expand to fill the row. It requires no media queries.

> **Grid alternative:** `grid-template-columns: repeat(auto-fit, minmax(18rem, 1fr));` is usually cleaner for equal columns. Flex wrap allows the last row's items to stretch to fill the width.

---

## 7. `order` and Accessibility

```css
.visually-first { order: -1; }
```

`order` changes **visual** order only. Keyboard focus and screen-reader order still follow the DOM. Never use `order` to create a meaningful content sequence that differs from the markup.

> **Accessibility rule:** DOM order must remain logical. Use `order` only for cosmetic rearrangement (e.g., moving a decorative badge).

---

## 8. Flexbox vs Grid: Choosing

| Situation | Use |
| :--- | :--- |
| Navigation bar, toolbar, button row | Flexbox |
| Card content that should fill available height | Flexbox |
| Unknown number of items in a line that should wrap | Flexbox |
| Page layout with header/sidebar/main/footer | Grid |
| Two-dimensional alignment (rows *and* columns) | Grid |
| Overlapping elements | Grid |
| Equal-height columns with precise track sizing | Grid |

> **Rule of thumb:** If you find yourself nesting three levels of flex containers to control both axes, switch to Grid.

---

## 9. Debugging Flexbox

1. In DevTools, inspect the container — Chrome overlays flex badges and gap sizes.
2. Check whether `min-width: 0` / `min-height: 0` is missing.
3. Verify `flex-wrap` before blaming `align-content`.
4. Remember that `margin: auto` on a flex item absorbs free space and can override alignment:

```css
/* Push the last item to the far right */
.toolbar > .spacer { margin-inline-start: auto; }
```

5. Inspect the computed `flex-basis` — it may not be what you expect.

---

## ✅ Checkpoint

- [ ] What is the difference between `justify-content` and `align-items`?
- [ ] Why does `flex: 1` sometimes make items narrower than their text?
- [ ] When does `align-content` have an effect?
- [ ] Why is `min-width: 0` needed for truncation in flex layouts?
- [ ] Why is `order` an accessibility risk?
