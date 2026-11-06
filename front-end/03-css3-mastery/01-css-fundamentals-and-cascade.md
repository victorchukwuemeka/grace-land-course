# CSS Fundamentals: Selectors, Specificity & the Cascade

CSS rules are not applied in the order you write them alone. The browser resolves conflicts using the **cascade**: origin, importance, cascade layers, specificity, scoping proximity, and finally source order.

---

## 1. Anatomy of a Rule

```css
/* selector { property: value; } */
.card__title {
  font-size: 1.5rem;
  line-height: 1.25;
  color: var(--color-text);
}

@media (min-width: 48rem) {
  .card__title {
    font-size: 2rem;
  }
}
```

- **Selector** — which elements match.
- **Declaration block** — the styles applied.
- **At-rule** — `@media`, `@supports`, `@layer`, `@container`, `@font-face`, `@keyframes`.

---

## 2. Selector Types

```css
/* Type / element */
p { color: #333; }

/* Class (reusable) */
.card { padding: 1rem; }

/* ID (unique, high specificity — use sparingly) */
#site-header { position: sticky; top: 0; }

/* Attribute */
a[href^="https"] { color: green; }
input[type="email"] { border-color: #ccc; }

/* Pseudo-class (state or position) */
a:hover { text-decoration: underline; }
li:nth-child(odd) { background: #f7f7f7; }
:focus-visible { outline: 2px solid #2563eb; }

/* Pseudo-element (generated box) */
.price::before { content: "$"; }
.clearfix::after { content: ""; display: block; clear: both; }

/* Combinators */
nav > ul { display: flex; }          /* direct child */
h2 + p { margin-top: 0; }            /* next sibling */
h2 ~ p { color: #555; }              /* any following sibling */
.card .title { font-weight: 700; }   /* descendant */
```

### Specificity in one table

| Selector | Specificity (A, B, C) |
| :--- | :--- |
| `*` | (0, 0, 0) |
| `li` | (0, 0, 1) |
| `.card` | (0, 1, 0) |
| `li:nth-child(2)` | (0, 1, 1) |
| `#header` | (1, 0, 0) |
| `a:hover` | (0, 1, 1) |
| Inline `style=""` | (1, 0, 0, 0) — beats all selectors |

> **Rule of thumb:** Keep specificity flat. Prefer one class per element over long descendant chains. IDs are for anchors and JavaScript hooks, not styling.

---

## 3. The Cascade Order (Highest to Lowest Priority)

1. **Origin + importance**
   - User-agent `!important` (browser accessibility overrides)
   - User `!important`
   - Author `!important`
   - Author normal
   - User normal
   - User-agent normal
2. **Cascade layers** — later `@layer` wins over earlier layers; unlayered styles win over layered styles.
3. **Specificity** — higher wins.
4. **Scoping proximity** — closer `@scope` root wins.
5. **Source order** — later wins when everything else is equal.

```css
/* Later layer wins, regardless of selector specificity */
@layer reset, base, components, utilities;

@layer components {
  .button { background: #2563eb; }
}

@layer utilities {
  .bg-red { background: #dc2626; }
}
```

---

## 4. Inheritance

Some properties inherit by default (typography, color, list style, cursor, visibility); most layout properties do not.

```css
body {
  font-family: system-ui, sans-serif;
  color: #1f2937;
  line-height: 1.6;
}

/* Force inheritance for a non-inherited property */
.form-field label {
  color: inherit;
}

/* Reset inheritance */
button {
  font: inherit;
}
```

### The `inherit`, `initial`, `unset`, and `revert` keywords

| Keyword | Meaning |
| :--- | :--- |
| `inherit` | Use the parent's computed value. |
| `initial` | Use the property's specification default. |
| `unset` | Inherit if naturally inherited, otherwise `initial`. |
| `revert` | Roll back to the previous origin/layer. |

---

## 5. Units

### Absolute

```css
border: 1px solid #ddd; /* device-independent pixel */
```

### Relative

| Unit | Relative to |
| :--- | :--- |
| `%` | Parent's corresponding dimension |
| `em` | Current element's font size (or parent's for `font-size`) |
| `rem` | Root (`html`) font size — best for spacing and typography |
| `ch` | Width of the `0` glyph |
| `lh` | Current line height |
| `vw` / `vh` | Viewport width / height |
| `dvh` / `svh` / `lvh` | Dynamic / small / large viewport height (mobile browser bars) |
| `fr` | Fraction of remaining grid/flex space |

```css
html { font-size: 100%; }         /* respect user preference (usually 16px) */
h1   { font-size: 2.5rem; }       /* 40px at default root size */
.card { padding: 1.5rem; }
.hero { min-height: 100dvh; }     /* avoids mobile URL-bar jump */
```

> **Accessibility:** Never set `html { font-size: 62.5%; }` just to make `rem` math easy. It overrides the user's browser font-size preference. Use `rem` with a 16px mental model instead.

---

## 6. Colors and Modern Color Functions

```css
:root {
  --brand-500: #2563eb;
  --brand-600: #1d4ed8;
  --surface: #ffffff;
  --text: #111827;
}

.alert {
  /* Modern space-separated syntax supports an alpha channel */
  background: rgb(220 38 38 / 0.1);
  border-left: 4px solid hsl(0 72% 51%);
  color: oklch(0.45 0.18 25);
}
```

- Use **hex** for static values, **`rgb()`/`hsl()`** for transparency, and **`oklch()`** when you need perceptually uniform palettes.
- Always verify contrast: WCAG AA requires **4.5:1** for normal text and **3:1** for large text.

---

## 7. Typography Essentials

```css
body {
  font-family: "Inter", system-ui, -apple-system, "Segoe UI", sans-serif;
  font-size: 1rem;
  line-height: 1.6;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
}

.prose {
  max-width: 68ch; /* comfortable reading measure */
}

h1, h2, h3 {
  line-height: 1.15;
  text-wrap: balance; /* avoid ugly single-word last lines */
}

p {
  text-wrap: pretty;
  hyphens: auto;
}
```

> **Gotcha:** `line-height` is inherited as a computed number when unitless, which is what you want. A fixed `line-height: 24px` will look cramped on large headings.

---

## 8. Debugging the Cascade

1. Open **DevTools → Elements → Styles**.
2. Crossed-out declarations are overridden — hover the rule to see which selector won.
3. Use the **Computed** tab to see the final value and its source.
4. Toggle declarations on/off instead of rewriting blindly.
5. Search for `!important` in the codebase before adding another one.

```css
/* Temporary debugging: outline every box */
* { outline: 1px solid rgb(255 0 0 / 0.25); }
```

---

## ✅ Checkpoint

- [ ] Can you calculate the specificity of `.nav > ul li.active`?
- [ ] Do you know why `.card .title` is usually worse than `.card__title`?
- [ ] Can you explain the difference between `em` and `rem`?
- [ ] Do you know why `font-size: 62.5%` on `html` is an accessibility smell?
- [ ] Can you fix an override without reaching for `!important`?
