# Responsive Design & Media Queries

Responsive design is not "make it smaller on mobile." It is building one fluid system that adapts to any viewport, input method, and user preference.

---

## 1. Mobile-First vs Desktop-First

**Mobile-first (recommended):** write base styles for small screens, then add complexity with `min-width` queries.

```css
/* Base: mobile */
.grid { display: grid; gap: 1rem; }

/* Tablet and up */
@media (min-width: 48rem) {
  .grid { grid-template-columns: repeat(2, 1fr); }
}

/* Desktop and up */
@media (min-width: 64rem) {
  .grid { grid-template-columns: repeat(3, 1fr); }
}
```

**Desktop-first:** base styles for large screens, then override with `max-width`. It tends to accumulate overrides and is harder to maintain.

| Strategy | Query | Cascade effect |
| :--- | :--- | :--- |
| Mobile-first | `min-width` | Progressive enhancement |
| Desktop-first | `max-width` | Progressive removal |

> **Rule:** Pick one strategy per project. Mixing both creates unpredictable cascade order.

---

## 2. Breakpoints Should Follow Content

Do not copy device widths blindly. Add a breakpoint **when the content starts to look wrong**.

```css
/* Content-driven breakpoints */
@media (min-width: 40rem) { /* ~640px */ }
@media (min-width: 48rem) { /* ~768px */ }
@media (min-width: 64rem) { /* ~1024px */ }
@media (min-width: 80rem) { /* ~1280px */ }
```

```css
/* Prefer rem in queries: they respond to user font-size settings */
@media (min-width: 48rem) { ... }
```

> **Gotcha:** `px` media queries ignore browser zoom in some contexts and do not respect user font preferences. Use `rem` or `em` where possible.

---

## 3. Fluid Layout Without Media Queries

Modern CSS can handle many responsive cases intrinsically:

```css
/* Fluid container */
.container {
  width: min(100% - 2rem, 72rem);
  margin-inline: auto;
}

/* Fluid type: 1rem at 320px, 2.5rem at 1200px */
h1 {
  font-size: clamp(1.75rem, 1rem + 3vw, 2.5rem);
}

/* Fluid spacing */
.section {
  padding-block: clamp(2rem, 5vw, 6rem);
}

/* Responsive grid without queries */
.auto-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(18rem, 100%), 1fr));
  gap: clamp(1rem, 2vw, 2rem);
}
```

### `clamp()` anatomy

```css
font-size: clamp(MIN, PREFERRED, MAX);
```

- `MIN` and `MAX` are hard limits.
- `PREFERRED` is usually a fluid value combining `rem` and `vw`.
- Keep the preferred value between min and max to avoid jumps.

> **Accessibility:** Never set `font-size` in `vw` alone. It prevents users from zooming text. Always clamp against a `rem` minimum.

---

## 4. Responsive Images

```html
<img
  src="photo-800.jpg"
  srcset="photo-400.jpg 400w, photo-800.jpg 800w, photo-1600.jpg 1600w"
  sizes="(min-width: 64rem) 50vw, 100vw"
  alt="A developer working at a standing desk"
  width="800"
  height="450"
  loading="lazy"
  decoding="async"
/>
```

- `srcset` + `sizes` let the browser choose the smallest sufficient file.
- Always set `width` and `height` (or `aspect-ratio`) to prevent layout shift.
- Use `loading="lazy"` for below-the-fold images.
- Use `<picture>` for art direction or modern formats:

```html
<picture>
  <source type="image/avif" srcset="hero.avif" />
  <source type="image/webp" srcset="hero.webp" />
  <img src="hero.jpg" alt="..." width="1600" height="900" />
</picture>
```

---

## 5. Container Queries

Media queries respond to the **viewport**. Container queries respond to the **parent container's size** — ideal for reusable components.

```css
.card-wrapper {
  container-type: inline-size;
  container-name: card;
}

.card {
  display: grid;
  gap: 1rem;
}

@container card (min-width: 30rem) {
  .card {
    grid-template-columns: 12rem 1fr;
    align-items: center;
  }
}
```

```css
/* Container query units */
.card__title {
  font-size: clamp(1rem, 4cqi, 1.5rem); /* cqi = 1% of container inline size */
}
```

> **Why it matters:** The same card component can render as a horizontal row in a wide sidebar and a vertical stack in a narrow column, without knowing anything about the viewport.

---

## 6. Responsive Typography

```css
:root {
  --step-0: clamp(1rem, 0.95rem + 0.25vw, 1.125rem);
  --step-1: clamp(1.25rem, 1.1rem + 0.75vw, 1.5rem);
  --step-2: clamp(1.5rem, 1.25rem + 1.25vw, 2rem);
  --step-3: clamp(2rem, 1.5rem + 2.5vw, 3rem);
}

body { font-size: var(--step-0); }
h1   { font-size: var(--step-3); }
h2   { font-size: var(--step-2); }
```

Use a modular scale (1.125, 1.25, or 1.333) for consistent hierarchy.

---

## 7. Responsive Navigation Patterns

### Disclosure menu (no JavaScript needed for a simple toggle)

```html
<details class="nav">
  <summary>Menu</summary>
  <ul>
    <li><a href="#courses">Courses</a></li>
    <li><a href="#projects">Projects</a></li>
  </ul>
</details>
```

```css
@media (min-width: 48rem) {
  .nav summary { display: none; }
  .nav ul {
    display: flex;
    gap: 1.5rem;
    list-style: none;
  }
}
```

> **Accessibility:** `<details>`/`<summary>` gives you keyboard support and expanded/collapsed semantics for free. If you build a custom menu, you must manage `aria-expanded`, focus, and Escape handling yourself.

---

## 8. Pointer and Preference Queries

```css
/* Touch devices: larger hit targets */
@media (pointer: coarse) {
  .button { min-height: 44px; }
}

/* Hover-capable devices only */
@media (hover: hover) and (pointer: fine) {
  .card:hover { transform: translateY(-2px); }
}

/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  :root {
    --surface: #0f172a;
    --text: #e2e8f0;
  }
}

/* High contrast */
@media (prefers-contrast: more) {
  :root { --border: #000; }
}
```

---

## 9. Testing Responsiveness

1. **DevTools device toolbar** (`Ctrl+Shift+M` / `Cmd+Shift+M`) — test 320px, 375px, 768px, 1024px, 1440px.
2. **Resize slowly**, not just at breakpoints. Bugs often appear between 640–700px.
3. Test with **large text** (browser zoom 200%) and **reduced motion**.
4. Test landscape orientation on mobile.
5. Check horizontal overflow:

```css
/* Temporary debug: highlight elements wider than the viewport */
* { outline: 1px solid red; }
```

6. Use `document.documentElement.scrollWidth > window.innerWidth` in the console to detect overflow.

---

## 10. Common Responsive Mistakes

| Mistake | Fix |
| :--- | :--- |
| Fixed widths (`width: 1200px`) | `max-width` + `width: 100%` |
| `height: 100vh` on mobile | `min-height: 100dvh` |
| Tiny touch targets | Minimum 44×44px for interactive elements |
| Hover-only interactions | Add focus and touch equivalents |
| Text in `vw` only | `clamp()` with a `rem` minimum |
| Hiding content on mobile with `display: none` | Reprioritize content; do not remove information |
| `user-scalable=no` in viewport meta | Never disable zoom |

---

## ✅ Checkpoint

- [ ] Why is mobile-first usually easier to maintain?
- [ ] What does `clamp(1rem, 2.5vw, 2rem)` do?
- [ ] When should you use a container query instead of a media query?
- [ ] Why should images have explicit `width` and `height`?
- [ ] What does `prefers-reduced-motion` protect against?
