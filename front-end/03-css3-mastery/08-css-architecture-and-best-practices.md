# CSS Architecture & Production Best Practices

At scale, CSS problems are rarely about syntax. They are about **ownership, naming, specificity, and dead code**. A good architecture makes it obvious where a style belongs and impossible for unrelated changes to break a component.

---

## 1. Principles

1. **Single responsibility** — one class, one job.
2. **Low specificity** — one class selector is usually enough.
3. **Composition over inheritance** — combine small classes instead of deep overrides.
4. **Predictable naming** — a new developer should infer a class's scope from its name.
5. **No `!important`** — except for utilities or third-party overrides inside a layer.
6. **Delete dead CSS** — unused styles are a maintenance tax.

---

## 2. BEM (Block Element Modifier)

```css
/* Block: standalone component */
.card { }

/* Element: part of the block (double underscore) */
.card__title { }
.card__body { }
.card__footer { }

/* Modifier: variation (double dash) */
.card--featured { }
.card--compact { }
```

```html
<article class="card card--featured">
  <h2 class="card__title">CSS Architecture</h2>
  <div class="card__body">...</div>
  <footer class="card__footer">...</footer>
</article>
```

**Why it works:** every selector has the same low specificity, and names describe ownership rather than DOM location.

```css
/* Bad: location-dependent, brittle */
.sidebar > div > h2 { ... }

/* Good: component-owned */
.card__title { ... }
```

> **Gotcha:** Do not nest BEM names like `.card__body__title`. That is a sign the component should be split.

---

## 3. Utility-First CSS

Utilities are single-purpose classes composed in markup.

```css
.flex { display: flex; }
.items-center { align-items: center; }
.gap-2 { gap: 0.5rem; }
.p-4 { padding: 1rem; }
.text-center { text-align: center; }
```

```html
<div class="flex items-center gap-2 p-4">
  <img class="rounded-full" src="avatar.jpg" alt="" />
  <span class="text-center">Ada Lovelace</span>
</div>
```

**Pros:** no naming fatigue, tiny CSS, obvious overrides.
**Cons:** verbose markup, harder to enforce design consistency unless utilities derive from tokens.

> **Hybrid approach (recommended):** component classes for structure and complex states, utilities for spacing and one-off adjustments.

---

## 4. ITCSS Layers

ITCSS organizes CSS by **reach and specificity**, from generic to explicit:

```
styles/
├── 01-settings/      /* tokens: colors, spacing, fonts */
├── 02-tools/         /* mixins/functions (if using Sass) */
├── 03-generic/       /* reset, box-sizing, normalize */
├── 04-elements/      /* bare element selectors: h1, a, table */
├── 05-objects/       /* layout patterns: .o-container, .o-grid */
├── 06-components/    /* UI components: .c-card, .c-button */
├── 07-utilities/     /* helpers: .u-hidden, .u-text-center */
└── main.css          /* imports in order */
```

Modern equivalent with cascade layers:

```css
@layer reset, base, layout, components, utilities;
```

---

## 5. File Organization

### Small project

```
css/
├── reset.css
├── tokens.css
├── base.css
├── layout.css
├── components.css
└── utilities.css
```

### Feature-based (medium/large)

```
src/
├── styles/
│   ├── tokens.css
│   ├── reset.css
│   └── global.css
├── components/
│   ├── button/
│   │   └── button.css
│   └── card/
│       └── card.css
└── pages/
    └── dashboard.css
```

> **Rule:** colocate component styles with the component when using a build system. Keep global styles global.

---

## 6. Design Tokens

```css
:root {
  /* Color */
  --color-bg: #ffffff;
  --color-text: #111827;
  --color-muted: #6b7280;
  --color-border: #e5e7eb;
  --color-brand: #2563eb;
  --color-brand-hover: #1d4ed8;
  --color-danger: #dc2626;

  /* Spacing scale (4px base) */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-6: 1.5rem;
  --space-8: 2rem;

  /* Typography */
  --font-sans: "Inter", system-ui, sans-serif;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;

  /* Radius and elevation */
  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 1rem;
  --shadow-sm: 0 1px 2px rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 12px rgb(0 0 0 / 0.1);
}
```

> **Never** use magic numbers in components. If a value repeats three times, it belongs in a token.

---

## 7. Responsive Architecture

- Use a small set of breakpoints (3–5).
- Define them as custom media if your tooling supports it; otherwise document them in one file.
- Prefer intrinsic layout (`auto-fit`, `clamp()`, container queries) over many breakpoints.
- Name breakpoints by content behavior (`sm`, `md`, `lg`), not devices (`iphone`, `ipad`).

```css
/* Breakpoint reference (documented once) */
/* sm: 40rem | md: 48rem | lg: 64rem | xl: 80rem */
```

---

## 8. Performance

### Critical CSS

Inline the styles needed for the first viewport; load the rest asynchronously.

```html
<style>/* critical above-the-fold styles */</style>
<link rel="preload" href="/styles/main.css" as="style" onload="this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="/styles/main.css"></noscript>
```

### Reduce cost

- Avoid deep descendant selectors (`.a .b .c .d`) — matching cost grows.
- Prefer `transform`/`opacity` animations.
- Use `content-visibility: auto` for long off-screen sections.
- Subset fonts and use `font-display: swap`.
- Remove unused CSS with a build tool.

```css
.long-section {
  content-visibility: auto;
  contain-intrinsic-size: 1px 800px;
}
```

### Font loading

```css
@font-face {
  font-family: "Inter";
  src: url("/fonts/inter.woff2") format("woff2");
  font-display: swap;
  unicode-range: U+0000-00FF;
}
```

```html
<link rel="preload" href="/fonts/inter.woff2" as="font" type="font/woff2" crossorigin>
```

---

## 9. Accessibility Checklist

- [ ] Text contrast at least 4.5:1 (3:1 for large text).
- [ ] Focus styles visible on every interactive element.
- [ ] Hit targets at least 44×44px.
- [ ] Do not remove outlines without a replacement.
- [ ] Respect `prefers-reduced-motion`.
- [ ] Do not convey meaning through color alone.
- [ ] Support 200% browser zoom without horizontal scroll.
- [ ] Test keyboard navigation (Tab, Shift+Tab, Enter, Space, Escape).
- [ ] Use `:focus-visible`, not `:focus`, for mouse-vs-keyboard distinction.

```css
:focus-visible {
  outline: 2px solid var(--color-brand);
  outline-offset: 2px;
}
```

---

## 10. Debugging Strategy

| Symptom | First check |
| :--- | :--- |
| Style not applied | Selector match, file load, layer order, specificity |
| Element overflows | `box-sizing`, fixed width, `min-width: auto`, long unbroken text |
| Spacing looks wrong | Margin collapsing, gap vs margin, inherited line-height |
| Element hidden behind another | Stacking context, transformed ancestor, `z-index` |
| Sticky not working | Ancestor `overflow`, missing `align-self: start`, parent height |
| Layout jumps on load | Missing image dimensions, font swap, dynamic content |
| Mobile horizontal scroll | `100vw`, fixed widths, negative margins, long words |

```css
/* Overflow detective */
* { outline: 1px solid red; }

/* Find elements wider than the viewport */
```

```js
[...document.querySelectorAll("*")]
  .filter(el => el.getBoundingClientRect().right > window.innerWidth)
  .forEach(el => console.log(el));
```

---

## 11. Code Review Checklist

- [ ] No `!important` without a documented reason.
- [ ] No IDs used purely for styling.
- [ ] No magic colors or spacing values.
- [ ] Selectors are flat and component-scoped.
- [ ] Responsive behavior tested at 320px and 1440px.
- [ ] Focus and hover states implemented.
- [ ] Animations respect reduced motion.
- [ ] Unused styles removed.
- [ ] Class names follow the project convention.
- [ ] Dark mode (if supported) uses tokens, not duplicate rules.

---

## ✅ Checkpoint

- [ ] Can you explain BEM's block/element/modifier model?
- [ ] When would you choose utilities over component classes?
- [ ] What is the purpose of cascade layers in an architecture?
- [ ] How do design tokens prevent inconsistent UI?
- [ ] What are the first three things you check when a style "doesn't work"?
