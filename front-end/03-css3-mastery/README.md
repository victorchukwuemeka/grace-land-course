# Module 03: CSS3 Mastery & Modern Layouts

CSS is a declarative language: you describe *what* the result should look like, and the browser's rendering engine decides *how* to paint it. This module builds the mental models that make CSS predictable instead of a guessing game.

> **Core idea:** Every CSS bug is either a **cascade** problem (which rule wins?), a **layout** problem (which formatting context applies?), or a **containing block** problem (what is this element positioned relative to?).

---

## 📚 Module Contents

| File | What you will learn |
| :--- | :--- |
| [`01-css-fundamentals-and-cascade.md`](./01-css-fundamentals-and-cascade.md) | Selectors, specificity, inheritance, the cascade, units, colors, typography. |
| [`02-box-model-and-layout.md`](./02-box-model-and-layout.md) | Content/padding/border/margin, `box-sizing`, `display`, positioning, stacking contexts. |
| [`03-flexbox-mastery.md`](./03-flexbox-mastery.md) | Main/cross axis, growth and shrinkage, alignment, common layout patterns. |
| [`04-css-grid-mastery.md`](./04-css-grid-mastery.md) | Tracks, `fr`, `minmax()`, `auto-fit`, named areas, implicit grids, subgrid. |
| [`05-responsive-design-and-media-queries.md`](./05-responsive-design-and-media-queries.md) | Mobile-first design, breakpoints, fluid type, container queries, responsive images. |
| [`06-custom-properties-and-modern-css.md`](./06-custom-properties-and-modern-css.md) | Variables, `calc()`, `clamp()`, `:is()`/`:where()`, nesting, cascade layers, color functions. |
| [`07-animations-and-transitions.md`](./07-animations-and-transitions.md) | Transitions, transforms, keyframes, performance, `prefers-reduced-motion`. |
| [`08-css-architecture-and-best-practices.md`](./08-css-architecture-and-best-practices.md) | BEM, utility classes, design tokens, file organization, debugging strategy. |
| [`exercises/css-lab.md`](./exercises/css-lab.md) | Hands-on challenges with acceptance criteria. |

---

## 🎯 Learning Outcomes

By the end of this module you should be able to:

1. Predict which declaration wins without opening DevTools.
2. Build any common UI layout with Flexbox or Grid — and know which one to choose.
3. Explain why an element overflows, collapses, or stacks incorrectly.
4. Design mobile-first layouts that work from 320px to ultrawide screens.
5. Use custom properties to build a reusable design-token system.
6. Animate without triggering layout thrashing.
7. Organize CSS so it scales beyond a single developer.

---

## 🧠 The Three Questions to Ask When CSS "Doesn't Work"

1. **Is the rule actually applied?** Check the selector, the file load order, and the DevTools Styles pane (crossed-out declarations mean another rule won).
2. **What is the containing block?** Positioned elements resolve `top`/`left` against their nearest positioned ancestor, not necessarily their parent.
3. **Which layout context is active?** Normal flow, flex, grid, or absolutely positioned elements all calculate size differently.

> **Practice rule:** Never add `!important` to win a fight. Find the specificity conflict and fix the architecture instead.
