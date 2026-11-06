# Modules, Tooling & Production Best Practices

Writing JavaScript that works is only half the job. Production code must be modular, testable, secure, and fast. This file covers the workflow around the language.

---

## 1. ES Modules in Depth

```js
// math.js
export const PI = Math.PI;

export function add(a, b) {
  return a + b;
}

export default function multiply(a, b) {
  return a * b;
}
```

```js
// app.js
import multiply, { PI, add } from "./math.js";
import * as math from "./math.js";
```

### Rules

- Imports are **hoisted** and resolved before execution.
- Imports are **live read-only bindings** — if the exporting module updates a `let`, importers see the new value.
- Modules have their own scope and run in strict mode.
- A module executes **once**, no matter how many times it is imported.
- Use relative paths with extensions in the browser.

```js
// Circular imports can produce partially initialized modules.
// Prefer a dependency graph without cycles.
```

### Dynamic import

```js
button.addEventListener("click", async () => {
  const { openModal } = await import("./modal.js");
  openModal();
});
```

Use dynamic imports for route-level code splitting and heavy optional features.

---

## 2. Project Structure

```
src/
├── index.html
├── main.js
├── styles/
│   ├── tokens.css
│   └── main.css
├── components/
│   ├── button.js
│   └── modal.js
├── pages/
│   ├── home.js
│   └── dashboard.js
├── lib/
│   ├── api.js
│   ├── storage.js
│   └── dom.js
└── utils/
    └── format.js
```

Principles:

- **Feature folders** scale better than type-only folders on large apps.
- **`lib/`** contains framework-agnostic utilities.
- **`components/`** contains UI and behavior.
- **No deep relative imports** (`../../../`); use path aliases configured in the bundler.

---

## 3. Bundlers and Dev Servers

| Tool | Role |
| :--- | :--- |
| **Vite** | Fast dev server + production bundler (recommended for vanilla and frameworks) |
| **esbuild** | Extremely fast bundler/minifier |
| **Rollup** | Library bundling |
| **Webpack** | Mature, highly configurable bundler |
| **Parcel** | Zero-config bundler |

```bash
npm create vite@latest my-app -- --template vanilla
cd my-app
npm install
npm run dev
npm run build
npm run preview
```

Vite gives you:

- Native ES modules in development (no bundling).
- Hot module replacement.
- Asset handling and CSS imports.
- Optimized production builds with tree shaking.

> **For this course:** you do not need a bundler to learn the language. Add one when you need npm packages, TypeScript, or production optimization.

---

## 4. Linting and Formatting

### ESLint

```bash
npm install -D eslint @eslint/js
npx eslint --init
```

```js
// eslint.config.js (flat config)
import js from "@eslint/js";

export default [
  js.configs.recommended,
  {
    rules: {
      "no-unused-vars": "warn",
      "no-undef": "error",
      eqeqeq: "error",
      "prefer-const": "warn",
    },
  },
];
```

### Prettier

```bash
npm install -D prettier
npx prettier --write .
```

```json
{
  "semi": true,
  "singleQuote": false,
  "printWidth": 90,
  "trailingComma": "all"
}
```

> **Rule:** Let Prettier own formatting and ESLint own correctness. Do not fight about spaces in code review.

---

## 5. Testing

### Unit tests with Vitest

```bash
npm install -D vitest
```

```js
// math.test.js
import { describe, it, expect } from "vitest";
import { add } from "./math.js";

describe("add", () => {
  it("adds two numbers", () => {
    expect(add(2, 3)).toBe(5);
  });

  it("handles negative numbers", () => {
    expect(add(-1, 1)).toBe(0);
  });
});
```

### DOM tests

```js
// @vitest-environment jsdom
import { renderCounter } from "./counter.js";

it("increments on click", () => {
  document.body.innerHTML = '<button id="inc">+</button><output id="out">0</output>';
  renderCounter();
  document.querySelector("#inc").click();
  expect(document.querySelector("#out").textContent).toBe("1");
});
```

### The testing pyramid

```
        ▲  E2E (Playwright/Cypress) — few, slow, high confidence
       ███ Integration — some, medium speed
     █████ Unit — many, fast, isolated
```

Test behavior, not implementation details. Prefer queries by role and label (accessible names) over CSS selectors.

---

## 6. Debugging

```js
console.log({ user, posts }); // labeled object
console.table(users);
console.time("fetch");
await fetchData();
console.timeEnd("fetch");
console.assert(total > 0, "Total should be positive");
console.trace("Where am I?");
debugger; // pauses in DevTools Sources
```

### Breakpoints and the call stack

1. Open DevTools → **Sources**.
2. Set a breakpoint on the line.
3. Trigger the code.
4. Inspect scope, watch expressions, and call stack.
5. Step over (`F10`), into (`F11`), out (`Shift+F11`).

> **Tip:** Use conditional breakpoints (`count > 10`) and logpoints to avoid editing code just to inspect state.

---

## 7. Performance

### Measure first

```js
performance.mark("start");
doWork();
performance.mark("end");
performance.measure("work", "start", "end");
```

Use the **Performance** panel to record and inspect:

- Long tasks (> 50ms).
- Layout shifts (CLS).
- Forced synchronous layouts.
- Garbage collection pauses.

### Core Web Vitals

| Metric | Measures | Good |
| :--- | :--- | :--- |
| **LCP** | Largest contentful paint | ≤ 2.5s |
| **INP** | Interaction to next paint | ≤ 200ms |
| **CLS** | Cumulative layout shift | ≤ 0.1 |

### Practical optimizations

```js
// Debounce expensive input handlers
input.addEventListener("input", debounce(search, 300));

// Lazy-load heavy modules
const { parse } = await import("./parser.js");

// Use requestIdleCallback for non-urgent work
requestIdleCallback(() => prefetchNextPage());

// Avoid layout thrashing
const width = el.offsetWidth;
el.style.width = `${width * 2}px`;
```

- Ship less JavaScript.
- Split code by route.
- Defer non-critical scripts.
- Cache API responses.
- Use `content-visibility` for off-screen sections.

---

## 8. Security

### XSS (Cross-Site Scripting)

```js
// Never do this with untrusted input
el.innerHTML = `<p>${userComment}</p>`;

// Safe
el.textContent = userComment;
```

If you must render HTML, sanitize it:

```js
import DOMPurify from "dompurify";
el.innerHTML = DOMPurify.sanitize(untrustedHTML);
```

### Other risks

| Risk | Mitigation |
| :--- | :--- |
| XSS | Escape output, sanitize HTML, CSP |
| CSRF | SameSite cookies, CSRF tokens |
| Clickjacking | `X-Frame-Options`, CSP `frame-ancestors` |
| Prototype pollution | Validate object keys, avoid recursive merge of untrusted objects |
| Supply chain | Lockfiles, audit dependencies, pin versions |
| Secrets in bundle | Never put secrets in frontend code |

### Content Security Policy

```http
Content-Security-Policy: default-src 'self'; img-src 'self' https: data:; script-src 'self'
```

CSP is a strong second line of defense against XSS, but it must be configured by the server.

---

## 9. Accessibility and JavaScript

- Prefer native elements; add ARIA only when needed.
- Manage focus for modals, menus, and route changes.
- Announce dynamic updates with `aria-live`.
- Support keyboard interaction (Enter, Space, Escape, arrows).
- Never rely on hover alone.
- Respect `prefers-reduced-motion`.

```js
function announce(message) {
  const region = document.querySelector("[aria-live]");
  region.textContent = message;
}
```

---

## 10. Git and Code Review

- Commit small, focused changes with imperative messages: `Add debounce to search input`.
- Do not commit `node_modules`, `.env`, or build artifacts.
- Review your own diff before requesting review.
- Include tests and screenshots for UI changes.
- Keep PRs under ~400 lines when possible.

```gitignore
node_modules/
dist/
.env
.DS_Store
```

---

## 11. Production Checklist

- [ ] No `console.log` in production code (use a logger).
- [ ] Errors are caught and reported.
- [ ] Loading/error/empty states exist.
- [ ] Bundle is split and minified.
- [ ] Images are optimized and lazy-loaded.
- [ ] Accessibility audit passes.
- [ ] HTTPS everywhere.
- [ ] CSP and security headers configured.
- [ ] Dependencies audited (`npm audit`).
- [ ] Tests run in CI.
- [ ] Source maps available for debugging.
- [ ] Analytics and monitoring are privacy-respecting.

---

## ✅ Checkpoint

- [ ] What is the difference between default and named exports?
- [ ] Why do ES modules execute once?
- [ ] When would you use a dynamic import?
- [ ] What should you unit test vs. E2E test?
- [ ] How does XSS differ from CSRF?
- [ ] Which Core Web Vital measures layout stability?
