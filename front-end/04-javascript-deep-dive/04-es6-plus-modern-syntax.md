# ES6+ Modern Syntax

Modern JavaScript is expressive and concise, but every feature has trade-offs. This file covers the syntax you will use daily and the semantics that trip people up.

---

## 1. Destructuring

### Objects

```js
const user = { name: "Ada", age: 36, address: { city: "London" } };

const { name, age } = user;
const { name: fullName } = user;                 // rename
const { country = "UK" } = user;                 // default
const { address: { city } } = user;              // nested
const { name: firstName, ...rest } = user;       // rest
```

### Arrays

```js
const [first, second] = ["a", "b", "c"];
const [, , third] = ["a", "b", "c"]; // skip
const [head, ...tail] = [1, 2, 3, 4];
const [x = 0, y = 0] = [];           // defaults
```

### Function parameters

```js
function draw({ x = 0, y = 0, color = "black" }) {
  return { x, y, color };
}

// Swap variables without a temp
let a = 1, b = 2;
[a, b] = [b, a];
```

> **Gotcha:** Destructuring `null` or `undefined` throws. Use a default on the whole parameter: `function f({ x } = {}) {}`.

---

## 2. Spread and Rest

The same `...` syntax means different things depending on position.

```js
// Spread: expand an iterable or object
const nums = [1, 2, 3];
Math.max(...nums);          // 3
const copy = [...nums];
const merged = { ...obj1, ...obj2 };

// Rest: collect remaining values
function sum(...values) { return values.reduce((a, b) => a + b, 0); }
const [first, ...others] = nums;
```

> **Deep vs shallow:** Spread copies one level. Nested objects and arrays are still shared.

---

## 3. Template Literals

```js
const name = "Ada";
const greeting = `Hello, ${name}!`;

// Multi-line
const html = `
  <article class="card">
    <h2>${title}</h2>
    <p>${body}</p>
  </article>
`;

// Tagged templates
function highlight(strings, ...values) {
  return strings.reduce((out, str, i) => {
    const value = values[i] ? `<mark>${values[i]}</mark>` : "";
    return out + str + value;
  }, "");
}

highlight`Hello ${name}, you have ${3} messages`;
```

> **Security:** Never interpolate untrusted data into HTML without escaping. Template literals do not sanitize.

---

## 4. Enhanced Object Literals

```js
const name = "Ada";
const age = 36;

const user = {
  name,                 // shorthand property
  age,
  greet() { return this.name; }, // shorthand method
  [`key_${age}`]: true, // computed property
};
```

---

## 5. Optional Chaining and Nullish Coalescing

```js
const city = user?.address?.city;
const result = api?.getData?.();
const first = list?.[0];

// Only null/undefined trigger the fallback
const count = response.count ?? 0;
```

```js
// Optional call with args
callback?.(value);

// Optional delete
delete user?.temporary;
```

> **Gotcha:** `??` cannot be mixed with `||` or `&&` without parentheses:
> `a ?? b || c` is a syntax error. Write `(a ?? b) || c`.

---

## 6. Array Methods

```js
const products = [
  { name: "Keyboard", price: 80, inStock: true },
  { name: "Mouse", price: 30, inStock: false },
  { name: "Monitor", price: 250, inStock: true },
];

products.map((p) => p.name);                       // transform
products.filter((p) => p.inStock);                 // select
products.find((p) => p.price > 100);               // first match
products.findIndex((p) => p.name === "Mouse");     // index or -1
products.some((p) => p.price > 200);               // true
products.every((p) => p.price > 0);                // true
products.reduce((total, p) => total + p.price, 0); // 360
products.sort((a, b) => a.price - b.price);        // mutates!
products.toSorted((a, b) => a.price - b.price);    // non-mutating (modern)
products.flatMap((p) => p.name.split(""));         // map + flatten
```

### Chaining

```js
const total = products
  .filter((p) => p.inStock)
  .map((p) => p.price)
  .reduce((sum, price) => sum + price, 0);
```

| Method | Mutates? | Returns |
| :--- | :--- | :--- |
| `map` | No | New array |
| `filter` | No | New array |
| `reduce` | No | Accumulated value |
| `sort` | **Yes** | Same array |
| `reverse` | **Yes** | Same array |
| `splice` | **Yes** | Removed items |
| `slice` | No | New array |
| `toSorted` | No | New array |

> **Gotcha:** `sort()` converts elements to strings by default. Always pass a comparator for numbers: `(a, b) => a - b`.

### `reduce` mental model

```js
array.reduce((accumulator, current, index, array) => {
  return nextAccumulator;
}, initialValue);
```

Always provide an initial value. Without it, an empty array throws.

---

## 7. `Map`, `Set`, `WeakMap`, `WeakSet`

### `Map`

```js
const cache = new Map();
cache.set("user:1", { name: "Ada" });
cache.get("user:1");
cache.has("user:1");
cache.delete("user:1");
cache.size;

// Preserves insertion order; keys can be any type
const key = {};
cache.set(key, "object key");
```

### `Set`

```js
const unique = new Set([1, 2, 2, 3]); // Set {1, 2, 3}
unique.add(4);
unique.has(3);
unique.size;
[...unique]; // [1, 2, 3, 4]

// Remove duplicates
const deduped = [...new Set(array)];
```

### Weak variants

```js
const wm = new WeakMap(); // keys must be objects; does not prevent GC
const ws = new WeakSet();
```

Use `WeakMap` for attaching private data to objects without preventing garbage collection.

> **When to use `Map` over an object:** keys are not strings, you need insertion order guarantees, frequent additions/deletions, or a known `size`.

---

## 8. Iterators and Generators

### Iterables

An object is iterable if it implements `Symbol.iterator`.

```js
const range = {
  from: 1,
  to: 5,
  [Symbol.iterator]() {
    let current = this.from;
    const last = this.to;
    return {
      next() {
        return current <= last
          ? { value: current++, done: false }
          : { value: undefined, done: true };
      },
    };
  },
};

[...range]; // [1, 2, 3, 4, 5]
```

### Generators

```js
function* idGenerator() {
  let id = 1;
  while (true) {
    yield id++;
  }
}

const ids = idGenerator();
ids.next(); // { value: 1, done: false }
ids.next(); // { value: 2, done: false }
```

```js
// Delegation
function* combined() {
  yield* [1, 2];
  yield* "ab";
}
[...combined()]; // [1, 2, "a", "b"]
```

Generators are useful for lazy sequences, custom iteration, and state machines. `async`/`await` largely replaces their async use cases.

---

## 9. ES Modules

```js
// math.js
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export default function multiply(a, b) { return a * b; }

// app.js
import multiply, { PI, add } from "./math.js";
import * as math from "./math.js";
```

```html
<script type="module" src="app.js"></script>
```

- Modules are strict mode by default.
- Top-level bindings are module-scoped, not global.
- Imports are **live bindings** and hoisted.
- `import()` dynamically loads a module and returns a promise.

```js
const { format } = await import("./formatters.js");
```

> **Gotcha:** Modules require a server (or correct MIME types). Opening `file://` with `type="module"` often fails due to CORS.

---

## 10. Modern Built-ins

```js
// Object.groupBy (modern)
const grouped = Object.groupBy(products, (p) => (p.inStock ? "in" : "out"));

// Array.from
Array.from({ length: 5 }, (_, i) => i * 2); // [0, 2, 4, 6, 8]

// Array.of
Array.of(7); // [7] (unlike new Array(7))

// flat / flatMap
[1, [2, [3]]].flat(2); // [1, 2, 3]

// at (negative indexing)
[1, 2, 3].at(-1); // 3

// findLast / findLastIndex
[1, 2, 3, 4].findLast((n) => n % 2 === 0); // 4
```

---

## 11. Strict Mode

```js
"use strict";
```

- Assigning to an undeclared variable throws.
- `this` is `undefined` in plain function calls.
- Duplicate parameter names are forbidden.
- `delete` on non-configurable properties throws.

ES modules and class bodies are strict automatically. Prefer modules.

---

## ✅ Checkpoint

- [ ] What is the difference between spread and rest?
- [ ] When does `??` differ from `||`?
- [ ] Which common array methods mutate the original?
- [ ] When is a `Map` better than an object?
- [ ] What does a generator's `yield` do?
- [ ] Why do ES modules need a server?
