# Custom Properties & Modern CSS

Modern CSS is a design system language, not just a list of colors. Custom properties, functions, nesting, and cascade layers let you build maintainable, themeable interfaces without a preprocessor.

---

## 1. Custom Properties (CSS Variables)

```css
:root {
  --color-brand: #2563eb;
  --color-text: #111827;
  --color-surface: #ffffff;
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 1rem;
  --space-4: 1.5rem;
  --radius-sm: 0.375rem;
  --radius-md: 0.75rem;
  --shadow-md: 0 4px 12px rgb(0 0 0 / 0.1);
}

.button {
  background: var(--color-brand);
  color: var(--color-surface);
  padding: var(--space-2) var(--space-3);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-md);
}
```

### Fallback values

```css
color: var(--color-text, #111827);
```

The fallback applies when the variable is **not defined**, not when it is invalid.

### Scoped variables

```css
.card {
  --card-padding: 1rem;
  padding: var(--card-padding);
}

.card--featured {
  --card-padding: 2rem;
}
```

Custom properties inherit, so a value set on a parent affects descendants. This is how theming works:

```css
[data-theme="dark"] {
  --color-text: #e2e8f0;
  --color-surface: #0f172a;
  --color-brand: #60a5fa;
}
```

```html
<html data-theme="dark">
```

> **Gotcha:** Variables are resolved at computed-value time. A variable containing `10px` used in `calc()` works, but a variable containing a full declaration (`--x: color: red`) does not — custom properties hold *values*, not declarations.

---

## 2. `calc()`, `min()`, `max()`, and `clamp()`

```css
.sidebar {
  width: calc(100% / 3 - 1rem);
}

.container {
  width: min(100% - 2rem, 70rem);
}

.fluid {
  font-size: clamp(1rem, 1vw + 0.75rem, 1.5rem);
}

.offset {
  margin-block-start: calc(var(--header-height) + var(--space-3));
}
```

Rules:
- Add spaces around `+` and `-`.
- Do not multiply by unitless values incorrectly: `calc(1rem * 1.5)` is valid; `calc(1rem * 1rem)` is not.
- `min()`/`max()` accept multiple comma-separated values.

---

## 3. `:is()`, `:where()`, and `:has()`

### `:is()` — grouping with the highest specificity inside

```css
:is(h1, h2, h3) + p {
  margin-top: 0.5rem;
}
```

### `:where()` — grouping with **zero** specificity

```css
:where(ul, ol) {
  padding-inline-start: 1.25rem;
}
```

Ideal for resets and base styles that should be easy to override.

### `:has()` — parent/relational selector

```css
/* Card that contains an image */
.card:has(img) {
  grid-template-columns: 12rem 1fr;
}

/* Form field with an invalid input */
.field:has(input:invalid) {
  color: #b91c1c;
}

/* Nav that contains an active link */
.nav:has(.is-active) {
  border-bottom-color: var(--color-brand);
}
```

> **Performance note:** `:has()` is supported in modern browsers. Avoid extremely broad `:has(*)` selectors in huge DOMs; scope them to a component.

---

## 4. CSS Nesting

```css
.card {
  padding: 1rem;
  border: 1px solid var(--color-border);

  &__title {
    font-size: 1.25rem;
  }

  &:hover {
    border-color: var(--color-brand);
  }

  @media (min-width: 48rem) {
    padding: 2rem;
  }
}
```

Native nesting uses `&` for the parent selector. It is not identical to Sass: nesting affects specificity the same way a descendant selector would. Keep nesting shallow (2–3 levels maximum).

---

## 5. Cascade Layers

Layers give you explicit control over the cascade **without** specificity wars.

```css
@layer reset, base, layout, components, utilities;

@layer reset {
  *, *::before, *::after { box-sizing: border-box; }
  body { margin: 0; }
}

@layer base {
  a { color: var(--color-brand); }
}

@layer components {
  .button { padding: 0.5rem 1rem; }
}

@layer utilities {
  .text-center { text-align: center; }
}
```

Rules:
- Later layers beat earlier layers.
- Unlayered styles beat all layered styles.
- Within a layer, normal specificity applies.

> **Use case:** Put third-party CSS in a low-priority layer so your component styles always win without `!important`.

```css
@import url("vendor.css") layer(vendor);

@layer vendor, components;
```

---

## 6. Modern Color

```css
:root {
  /* Perceptually uniform palette */
  --brand-50:  oklch(0.97 0.02 250);
  --brand-500: oklch(0.62 0.19 250);
  --brand-900: oklch(0.32 0.12 250);

  --success: oklch(0.65 0.17 150);
  --danger:  oklch(0.60 0.22 25);
}

/* Relative color syntax */
.button--ghost {
  background: rgb(from var(--brand-500) r g b / 0.1);
}

/* color-mix() */
.badge {
  background: color-mix(in oklch, var(--brand-500) 15%, white);
  color: color-mix(in oklch, var(--brand-500) 70%, black);
}
```

Use `color-mix()` to generate hover states and tints without hardcoding extra hex values.

---

## 7. Logical Properties and Writing Modes

```css
.card {
  margin-inline: auto;
  padding-block: 1.5rem;
  padding-inline: 1rem;
  border-inline-start: 4px solid var(--color-brand);
  inline-size: 100%;
  max-inline-size: 32rem;
}
```

These adapt automatically to `dir="rtl"` and vertical writing modes.

---

## 8. Modern Selectors Worth Knowing

```css
/* Attribute case-insensitive */
a[href$=".pdf" i] { ... }

/* Focus within a form group */
.field:focus-within { border-color: var(--color-brand); }

/* Placeholder shown */
input:placeholder-shown { ... }

/* Disabled and enabled */
button:disabled { opacity: 0.5; cursor: not-allowed; }

/* Target empty containers */
.list:empty::after { content: "No items yet"; }

/* Selection color */
::selection { background: var(--color-brand); color: white; }

/* Marker styling */
li::marker { color: var(--color-brand); }
```

---

## 9. `@supports` for Progressive Enhancement

```css
@supports (display: grid) {
  .layout { display: grid; }
}

@supports not (backdrop-filter: blur(1px)) {
  .overlay { background: rgb(0 0 0 / 0.8); }
}

@supports (container-type: inline-size) {
  .card-wrapper { container-type: inline-size; }
}
```

> **Philosophy:** Build a solid baseline, then enhance. Do not block the entire stylesheet on an experimental feature.

---

## 10. `prefers-*` and User Preferences

```css
@media (prefers-color-scheme: dark) { ... }
@media (prefers-reduced-motion: reduce) { ... }
@media (prefers-contrast: more) { ... }
@media (forced-colors: active) { ... }
```

```css
/* Respect forced colors: do not rely on background images for meaning */
@media (forced-colors: active) {
  .button { border: 1px solid ButtonText; }
}
```

---

## 11. Design Tokens in Practice

```css
:root {
  /* Primitive tokens */
  --blue-500: #2563eb;
  --gray-900: #111827;
  --space-3: 1rem;

  /* Semantic tokens */
  --color-action: var(--blue-500);
  --color-text: var(--gray-900);
  --space-card: var(--space-3);

  /* Component tokens */
  --button-bg: var(--color-action);
  --button-padding: 0.5rem 1rem;
}

.button {
  background: var(--button-bg);
  padding: var(--button-padding);
}
```

Three tiers — **primitive → semantic → component** — let you rebrand or theme by changing a small number of values.

---

## ✅ Checkpoint

- [ ] What is the difference between `:is()` and `:where()`?
- [ ] How do cascade layers change the specificity game?
- [ ] What does `clamp(1rem, 2vw, 2rem)` guarantee?
- [ ] When does a custom property fallback apply?
- [ ] How would you theme a component with scoped variables?
