# CSS Lab: Hands-On Challenges

Complete these challenges in order. Each one has **acceptance criteria** so you can self-verify. Create a folder `css-lab/` with one HTML file per challenge and a shared `styles.css`.

---

## Challenge 1: The Cascade Detective

Given the following CSS, predict the final color of the paragraph **before** running it. Then verify in DevTools.

```html
<div id="app" class="wrapper">
  <p class="text highlight">Hello</p>
</div>
```

```css
p { color: black; }
.text { color: blue; }
#app .text { color: green; }
p.highlight { color: orange; }
.wrapper p { color: purple; }
```

**Tasks:**
1. Write down the winning declaration and its specificity.
2. Add one rule that wins **without** using `!important` or an ID.
3. Explain why source order does not decide the winner here.

**Acceptance criteria:** You can justify the result using `(A, B, C)` specificity.

---

## Challenge 2: Box Model Math

Build a card with:

- Content width: `20rem`
- Horizontal padding: `1.5rem` each side
- Border: `2px` each side
- Margin: `2rem` vertical

**Tasks:**
1. Calculate the total rendered width with `content-box`.
2. Add `box-sizing: border-box` and recalculate.
3. Use DevTools to confirm both values.

**Acceptance criteria:** The card never exceeds its parent width on a 320px screen.

---

## Challenge 3: Navbar with Flexbox

Build a responsive navbar with:

- Brand on the left
- Three links on the right
- A "Sign in" button
- Vertical centering
- Links stack into a column under `48rem`

**Acceptance criteria:**
- No fixed heights.
- `gap` used for spacing, not margins between links.
- Keyboard focus is visible.
- No horizontal overflow at 320px.

---

## Challenge 4: Dashboard with Grid

Build a dashboard shell:

```
┌──────────────────────────────┐
│ Header                       │
├──────────┬───────────────────┤
│ Sidebar  │ Main content      │
│          │                   │
├──────────┴───────────────────┤
│ Footer                       │
└──────────────────────────────┘
```

**Tasks:**
1. Use `grid-template-areas`.
2. Sidebar collapses above the main content below `60rem`.
3. Sidebar is sticky on desktop.
4. Main content scrolls independently if it exceeds the viewport.

**Acceptance criteria:**
- `min-height: 100dvh` on the shell.
- No absolute positioning.
- Sticky sidebar works (`align-self: start`).

---

## Challenge 5: Responsive Card Gallery

Create a gallery of 9 cards using **no media queries**.

**Requirements:**
- Cards are at least `16rem` wide.
- They wrap automatically.
- Gap scales fluidly.
- Works at 320px without overflow.

**Acceptance criteria:** The grid uses `repeat(auto-fit, minmax(min(...), 1fr))`.

---

## Challenge 6: Theme Switcher

Build a page with light/dark themes using custom properties.

**Tasks:**
1. Define semantic tokens on `:root`.
2. Override them under `[data-theme="dark"]`.
3. Add a button that toggles `data-theme` on `<html>`.
4. Persist the choice in `localStorage`.
5. Respect `prefers-color-scheme` on first load.

**Acceptance criteria:**
- No component rule changes between themes.
- Contrast meets WCAG AA in both themes.
- The toggle has an accessible label and keyboard support.

---

## Challenge 7: Animated Modal

Build a modal that:

- Fades and scales in.
- Traps focus inside.
- Closes on Escape and backdrop click.
- Returns focus to the trigger.
- Respects `prefers-reduced-motion`.

**Acceptance criteria:**
- Uses `transform` and `opacity` only for animation.
- `role="dialog"` and `aria-modal="true"` are set.
- Background scroll is locked while open.
- No layout shift when it opens.

---

## Challenge 8: Responsive Article

Build a blog article layout with:

- A centered reading column (`68ch` max).
- A full-bleed hero image.
- A sticky table of contents on large screens.
- Fluid typography with `clamp()`.

**Acceptance criteria:**
- Text remains readable at 200% zoom.
- The TOC becomes a normal block below `64rem`.
- Images have explicit dimensions and `loading="lazy"` where appropriate.

---

## Challenge 9: Container Query Card

Build a card component that changes layout based on its **container**, not the viewport.

**Tasks:**
1. Set `container-type: inline-size` on a wrapper.
2. Below `30rem` container width, stack image above text.
3. Above `30rem`, place image beside text.
4. Put the same component in a narrow sidebar and a wide main area on the same page.

**Acceptance criteria:** Both instances adapt correctly at the same viewport width.

---

## Challenge 10: CSS Audit

Take any page you have built and audit it:

- [ ] Remove every `!important`.
- [ ] Replace magic numbers with tokens.
- [ ] Flatten selectors deeper than 3 levels.
- [ ] Add focus-visible styles.
- [ ] Add reduced-motion support.
- [ ] Fix contrast issues.
- [ ] Remove unused CSS.
- [ ] Verify 320px, 768px, 1440px, and 200% zoom.

**Deliverable:** A short before/after summary of what changed and why.

---

## Stretch: Build a Design System

Create `tokens.css`, `reset.css`, and component styles for:

- Button (primary, secondary, ghost, danger; sm/md/lg)
- Input, select, textarea, checkbox
- Card
- Badge
- Alert
- Modal
- Tabs

Document each component's states (default, hover, focus, active, disabled, loading) and accessibility notes.
