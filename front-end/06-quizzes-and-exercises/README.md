# Module 06: Quizzes & Exercises

Test your understanding before moving on. Answer first, then check the key at the bottom. Aim for **80% or higher** on each section.

---

## Part A: CSS Quiz (20 questions)

1. Which declaration wins?
   ```css
   p { color: blue; }
   .intro { color: green; }
   #hero p { color: red; }
   ```
   ```html
   <section id="hero"><p class="intro">Hi</p></section>
   ```

2. What is the rendered width of an element with `width: 200px; padding: 20px; border: 5px solid` under `content-box`?

3. What does `box-sizing: border-box` change?

4. Why does a child's `margin-top` sometimes push its parent down, and how do you prevent it?

5. What is the difference between `em` and `rem`?

6. What is the difference between `justify-content` and `align-items` in Flexbox?

7. Why does `flex: 1` sometimes cause text overflow, and what fixes it?

8. What is the difference between `auto-fit` and `auto-fill` in Grid?

9. How do you make a Grid item span all columns?

10. Why might `position: sticky` fail to work?

11. What creates a new stacking context?

12. Why can `z-index: 9999` still render behind another element?

13. What is the difference between `:is()` and `:where()`?

14. What does `clamp(1rem, 2.5vw, 2rem)` do?

15. Why is `font-size: 62.5%` on `html` considered an accessibility problem?

16. What is the difference between a media query and a container query?

17. Why is `transition: all` considered risky?

18. Which properties are safest to animate for performance?

19. What does `prefers-reduced-motion` protect against?

20. How do cascade layers change the specificity model?

---

## Part B: JavaScript Quiz (20 questions)

1. What are the two phases of an execution context?

2. What is the Temporal Dead Zone?

3. Why does `typeof null` return `"object"`?

4. What is the difference between `==` and `===`?

5. When should you use `??` instead of `||`?

6. Why is `0.1 + 0.2 !== 0.3`?

7. What is a closure? Give one practical use.

8. What are the five `this` binding rules?

9. Why do arrow functions not have their own `this`?

10. What does `new` do to a constructor function?

11. What is the difference between `Object.freeze` and a deep freeze?

12. What does `structuredClone` do that `JSON.parse(JSON.stringify())` does not?

13. What is the difference between `for...of` and `for...in`?

14. Which array methods mutate the original array?

15. What is the difference between a microtask and a macrotask?

16. Why does `Promise.resolve().then()` run before `setTimeout(..., 0)`?

17. Why does `forEach(async () => { await ... })` not wait?

18. When should you use `Promise.allSettled` instead of `Promise.all`?

19. Does `fetch` reject on a `404`? Why or why not?

20. Why should authentication tokens not be stored in `localStorage`?

---

## Part C: Practical Exercises

### CSS

1. Build a responsive 3-column card grid with no media queries.
2. Create a sticky header that does not break inside a transformed ancestor.
3. Build a modal with focus trapping and reduced-motion support.
4. Convert a fixed-width layout to a fluid one using `min()`, `clamp()`, and `rem`.
5. Implement a dark theme using only custom properties.

### JavaScript

1. Write `debounce` and `throttle` from scratch.
2. Implement `deepClone` without `structuredClone`.
3. Build a todo list with event delegation and `localStorage`.
4. Fetch two APIs in parallel and handle partial failure.
5. Explain and fix a memory leak caused by an event listener.

---

## Answer Key — CSS

1. **Red.** `#hero p` has specificity `(1, 0, 1)`, higher than `.intro` `(0, 1, 0)`.
2. **250px**: `200 + 40 + 10`.
3. Width includes padding and border; the content box shrinks to fit.
4. **Margin collapsing** through the parent. Prevent it with `padding-top`, `border`, `display: flow-root`, or a flex/grid context.
5. `em` is relative to the current/parent font size; `rem` is relative to the root font size.
6. `justify-content` aligns along the **main** axis; `align-items` aligns along the **cross** axis.
7. The default `min-width: auto` prevents shrinking below content size. Add `min-width: 0` (or `min-height: 0` in columns).
8. `auto-fit` collapses empty tracks so items stretch; `auto-fill` keeps them, leaving empty space.
9. `grid-column: 1 / -1`.
10. An ancestor with `overflow` other than `visible`, a missing `align-self: start`, or a parent without enough height.
11. Positioned elements with `z-index`, `opacity < 1`, transforms, filters, `isolation`, `contain`, and flex/grid children with `z-index`.
12. Because it is trapped inside a parent stacking context. The parent's `z-index` is what competes.
13. `:is()` takes the highest specificity of its arguments; `:where()` has zero specificity.
14. Sets a fluid value bounded between `1rem` and `2rem`, preferring `2.5vw`.
15. It overrides the user's browser font-size preference, breaking accessibility and zoom expectations.
16. Media queries respond to the viewport; container queries respond to the parent container's size.
17. It animates unintended properties, including layout-triggering ones, causing jank and surprise transitions.
18. `transform` and `opacity` (composited properties).
19. Vestibular disorders and motion sensitivity; it lets users request less animation.
20. Later layers beat earlier layers regardless of specificity; unlayered styles beat all layers.

---

## Answer Key — JavaScript

1. **Creation** (hoisting/declarations) and **execution** (line-by-line assignment).
2. The period between entering a scope and the declaration being initialized; accessing a `let`/`const` binding then throws a `ReferenceError`.
3. A historical bug from the first JavaScript implementation, preserved for backward compatibility.
4. `==` performs type coercion; `===` requires the same type and value.
5. When `0`, `""`, or `false` are valid values and should not trigger the fallback.
6. IEEE-754 floating-point cannot represent those decimals exactly.
7. A function that retains access to its lexical scope after the outer function returns. Uses: private state, factories, memoization, debounce.
8. Global/undefined, method call, explicit (`call`/`apply`/`bind`), constructor (`new`), and lexical (arrow).
9. They inherit `this` from the enclosing scope by design.
10. Creates an object, links its prototype to `constructor.prototype`, calls the constructor with `this`, and returns the object.
11. `Object.freeze` is shallow; nested objects remain mutable.
12. It clones `Date`, `Map`, `Set`, `ArrayBuffer`, handles cycles, and preserves more types.
13. `for...of` iterates values of an iterable; `for...in` iterates enumerable property keys (including inherited).
14. `sort`, `reverse`, `splice`, `push`, `pop`, `shift`, `unshift`, `fill`, `copyWithin`.
15. Microtasks run after the current task and before rendering; macrotasks run one per event-loop turn.
16. The microtask queue is drained before the next macrotask.
17. `forEach` ignores the returned promise; it does not await callbacks.
18. When partial success is acceptable and you need results from all requests.
19. No. `fetch` rejects only on network failure; HTTP errors still resolve with `response.ok === false`.
20. Any XSS can read `localStorage`. `HttpOnly` cookies are inaccessible to JavaScript.

---

## Scoring

| Score | Level |
| :--- | :--- |
| 36–40 | Ready to build production features. |
| 28–35 | Solid; review the missed topics. |
| 20–27 | Re-read the relevant modules and redo the labs. |
| Below 20 | Restart the module with the active-coding rule. |
