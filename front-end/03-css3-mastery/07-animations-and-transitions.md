# Transitions, Transforms & Keyframe Animations

Animation should communicate state, guide attention, and make interfaces feel responsive. It should never block interaction or trigger layout thrashing.

---

## 1. Transitions

A transition animates a property between two states.

```css
.button {
  background-color: var(--color-brand);
  transition: background-color 150ms ease, transform 150ms ease;
}

.button:hover {
  background-color: var(--color-brand-dark);
  transform: translateY(-1px);
}
```

### Transition properties

```css
.card {
  transition-property: transform, box-shadow, opacity;
  transition-duration: 200ms;
  transition-timing-function: cubic-bezier(0.2, 0, 0, 1);
  transition-delay: 0ms;
}

/* Shorthand */
.card {
  transition: transform 200ms cubic-bezier(0.2, 0, 0, 1),
              box-shadow 200ms ease;
}
```

| Timing function | Feel |
| :--- | :--- |
| `linear` | Mechanical; use for progress bars |
| `ease` | Default; gentle start and end |
| `ease-in` | Starts slow; good for exits |
| `ease-out` | Starts fast; good for entrances |
| `cubic-bezier()` | Custom curves |
| `steps()` | Discrete frames (sprites, typing) |

> **Gotcha:** `transition: all` animates properties you did not intend, including layout properties. Always list explicit properties.

---

## 2. Transforms

Transforms move, scale, rotate, or skew an element **without affecting layout**. They are composited and generally GPU-accelerated.

```css
.box {
  transform: translateX(2rem) rotate(5deg) scale(1.05);
  transform-origin: center;
}
```

| Function | Effect |
| :--- | :--- |
| `translate(x, y)` | Move |
| `scale(x, y)` | Resize |
| `rotate(angle)` | Rotate |
| `skew(x, y)` | Slant |
| `matrix()` | Combine all transforms |

```css
/* Modern individual transform properties */
.box {
  translate: 0 -4px;
  rotate: 2deg;
  scale: 1.05;
}
```

> **Important:** A transform creates a **containing block** for fixed/absolute descendants and a new **stacking context**. This is why fixed modals break inside transformed parents.

### 3D transforms

```css
.card {
  perspective: 1000px;
}

.card__inner {
  transform-style: preserve-3d;
  transition: transform 600ms ease;
}

.card:hover .card__inner {
  transform: rotateY(180deg);
}

.card__face {
  backface-visibility: hidden;
}
```

---

## 3. Keyframe Animations

```css
@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(12px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.hero__title {
  animation: fade-in-up 500ms cubic-bezier(0.2, 0, 0, 1) both;
}
```

### Animation properties

```css
.element {
  animation-name: pulse;
  animation-duration: 2s;
  animation-timing-function: ease-in-out;
  animation-delay: 200ms;
  animation-iteration-count: infinite;
  animation-direction: alternate;
  animation-fill-mode: both;
  animation-play-state: running;
}
```

| `animation-fill-mode` | Behavior |
| :--- | :--- |
| `none` | No styles applied outside the animation |
| `forwards` | Retains the last keyframe |
| `backwards` | Applies the first keyframe during the delay |
| `both` | Applies both |

```css
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50%      { opacity: 0.4; }
}

.skeleton {
  animation: pulse 1.5s ease-in-out infinite;
  background: linear-gradient(90deg, #eee, #f5f5f5, #eee);
  background-size: 200% 100%;
}
```

> **Gotcha:** Animating `display` does not work. For enter/exit animations, use `@starting-style`, `transition-behavior: allow-discrete`, or keep the element in the DOM and animate opacity/visibility.

---

## 4. Performance: Stick to `transform` and `opacity`

The browser rendering pipeline:

```
JavaScript → Style → Layout → Paint → Composite
```

Animating `width`, `height`, `top`, `left`, `margin`, or `font-size` triggers **layout** on every frame. Animating `transform` and `opacity` can usually skip straight to **composite**.

```css
/* Bad: animates layout */
.panel {
  transition: height 300ms ease;
}

/* Good: animates transform/opacity */
.panel {
  transform: translateY(-100%);
  transition: transform 300ms ease;
}

.panel.is-open {
  transform: translateY(0);
}
```

> **`will-change`:** Use it sparingly and remove it after the animation. `will-change: transform` on hundreds of elements wastes GPU memory.

```css
.modal {
  will-change: transform, opacity;
}
```

---

## 5. `prefers-reduced-motion`

```css
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
```

Better: design meaningful reduced-motion alternatives.

```css
.hero {
  animation: fade-in-up 500ms ease both;
}

@media (prefers-reduced-motion: reduce) {
  .hero {
    animation: fade-in 200ms ease both; /* no movement */
  }
}
```

> **Accessibility:** Motion can trigger vestibular disorders. Never make essential information depend on animation.

---

## 6. View Transitions (Progressive Enhancement)

```css
@view-transition {
  navigation: auto;
}

.card {
  view-transition-name: card;
}
```

```js
document.startViewTransition(() => {
  renderNewView();
});
```

Always feature-detect:

```js
if (document.startViewTransition) {
  document.startViewTransition(update);
} else {
  update();
}
```

---

## 7. Scroll-Driven Animations

```css
@keyframes grow {
  from { transform: scaleX(0); }
  to   { transform: scaleX(1); }
}

.progress {
  animation: grow linear;
  animation-timeline: scroll(root block);
  transform-origin: left;
}
```

Provide a static fallback for browsers without support.

---

## 8. Micro-Interaction Recipes

### Button press

```css
.button {
  transition: transform 120ms ease, background-color 120ms ease;
}

.button:active {
  transform: scale(0.97);
}
```

### Card lift

```css
.card {
  transition: transform 200ms ease, box-shadow 200ms ease;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px rgb(0 0 0 / 0.12);
}
```

### Loading spinner

```css
@keyframes spin {
  to { transform: rotate(360deg); }
}

.spinner {
  width: 1.5rem;
  aspect-ratio: 1;
  border: 2px solid rgb(0 0 0 / 0.15);
  border-top-color: var(--color-brand);
  border-radius: 50%;
  animation: spin 700ms linear infinite;
}
```

### Toast entrance

```css
@keyframes toast-in {
  from { opacity: 0; transform: translateY(1rem); }
  to   { opacity: 1; transform: translateY(0); }
}

.toast {
  animation: toast-in 250ms cubic-bezier(0.2, 0, 0, 1) both;
}
```

---

## 9. Debugging Animations

1. **DevTools → Animations** panel: slow down, inspect keyframes, and replay.
2. **Performance** panel: record and check for layout/paint work per frame.
3. Toggle `prefers-reduced-motion` in DevTools Rendering pane.
4. Ensure the element is not removed from the DOM before the exit animation finishes.
5. Check stacking contexts if an animated element jumps above/below others.

---

## ✅ Checkpoint

- [ ] Why is `transition: all` risky?
- [ ] Which two properties are safest to animate for performance?
- [ ] How do you animate an element being removed from the DOM?
- [ ] What does `animation-fill-mode: both` do?
- [ ] How do you respect `prefers-reduced-motion` without removing all feedback?
