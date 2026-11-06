# JavaScript Fundamentals & Execution

Before closures, promises, and frameworks, you need a precise model of how JavaScript runs. This file builds that model.

---

## 1. What JavaScript Is

- **High-level** — you do not manage memory manually.
- **Dynamically typed** — types are checked at runtime, not compile time.
- **Single-threaded** — one call stack, one instruction at a time.
- **JIT-compiled** — engines like V8 compile hot code to machine code.
- **Multi-paradigm** — supports procedural, object-oriented, and functional styles.
- **Prototype-based** — objects inherit directly from other objects.

---

## 2. The Runtime

```
┌──────────────────────────── JavaScript Runtime ────────────────────────────┐
│                                                                            │
│  ┌──────────────┐   ┌─────────────────────┐   ┌────────────────────────┐  │
│  │  Call Stack  │   │  Heap (objects)     │   │  Event Loop            │  │
│  │  (LIFO)      │   │                     │   │                        │  │
│  └──────────────┘   └─────────────────────┘   └───────────┬────────────┘  │
│                                                            │               │
│  ┌─────────────────────────────────────────────────────────▼────────────┐ │
│  │ Web APIs: setTimeout, fetch, DOM events, localStorage, ...           │ │
│  └─────────────────────────────────────────────────────────┬────────────┘ │
│                                                            │               │
│  ┌──────────────────────────┐   ┌──────────────────────────▼────────────┐ │
│  │ Microtask queue          │   │ Task (macrotask) queue                │ │
│  │ promises, queueMicrotask │   │ timers, I/O, UI events                │ │
│  └──────────────────────────┘   └───────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────────────────────┘
```

The event loop continuously checks: **is the call stack empty?** If yes, drain all microtasks, then run the next macrotask.

---

## 3. Execution Context

Every time code runs, it does so inside an **execution context**:

- **Global execution context** — created once.
- **Function execution context** — created on every function call.

Each context has:

1. **Variable environment** — `var` bindings, function declarations.
2. **Lexical environment** — `let`/`const` bindings, scope chain.
3. **`this` binding.**
4. **Outer environment reference** — the scope chain.

### Two phases

```js
console.log(a); // undefined (not ReferenceError)
var a = 10;
```

1. **Creation phase:** declarations are registered. `var a` is hoisted and initialized to `undefined`. Function declarations are fully hoisted.
2. **Execution phase:** assignments happen line by line.

```js
console.log(square(4)); // 16 — function declarations are fully hoisted
function square(n) { return n * n; }

console.log(cube(3)); // ReferenceError — function expression is not initialized
const cube = function (n) { return n ** 3; };
```

---

## 4. `var`, `let`, and `const`

```js
var  oldWay = "function-scoped, hoisted to undefined";
let  modern = "block-scoped, hoisted but in TDZ";
const fixed  = "block-scoped, must be initialized, cannot be reassigned";
```

| Feature | `var` | `let` | `const` |
| :--- | :--- | :--- | :--- |
| Scope | Function | Block | Block |
| Hoisted | Yes (`undefined`) | Yes (TDZ) | Yes (TDZ) |
| Redeclare in same scope | Yes | No | No |
| Reassign | Yes | Yes | No |
| Must initialize | No | No | Yes |

### Temporal Dead Zone (TDZ)

```js
{
  // TDZ starts here
  // console.log(name); // ReferenceError: Cannot access 'name' before initialization
  let name = "Ada";
  console.log(name); // "Ada"
}
```

The binding exists but is uninitialized until the declaration is evaluated.

> **Rule:** Use `const` by default. Use `let` only when reassignment is necessary. Avoid `var` in new code.

---

## 5. Primitive Types vs Objects

### Seven primitives

```js
"text"          // string
42              // number (IEEE-754 double)
9007199254740991n // bigint
true            // boolean
undefined       // undefined
null            // null
Symbol("id")    // symbol
```

Everything else is an **object** (arrays, functions, dates, maps, `null` is a historical exception).

```js
typeof "text";        // "string"
typeof 42;            // "number"
typeof 10n;           // "bigint"
typeof true;          // "boolean"
typeof undefined;     // "undefined"
typeof null;          // "object" (historical bug)
typeof Symbol();      // "symbol"
typeof {};            // "object"
typeof [];            // "object"
typeof function(){};  // "function"
```

### Primitives are immutable

```js
let name = "ada";
name.toUpperCase();  // returns "ADA"
console.log(name);   // "ada" — the original string is unchanged
name = name.toUpperCase(); // reassignment, not mutation
```

### Objects are reference values

```js
const a = { count: 1 };
const b = a;      // b points to the same object
b.count = 2;
console.log(a.count); // 2

const c = { ...a }; // shallow copy
c.count = 99;
console.log(a.count); // 2
```

---

## 6. Type Coercion

JavaScript converts types automatically in some operations.

```js
"5" + 1;   // "51"  (string concatenation wins)
"5" - 1;   // 4     (numeric subtraction)
true + 1;  // 2
[] + [];   // ""
[] + {};   // "[object Object]"
1 == "1";  // true  (loose equality coerces)
1 === "1"; // false (strict equality)
```

### Truthy and falsy

Falsy values: `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`.

Everything else is truthy — including `"0"`, `[]`, and `{}`.

```js
if ("0") console.log("truthy"); // runs
if ([]) console.log("truthy");  // runs
```

> **Rule:** Always use `===` and `!==`. Use `Object.is()` for edge cases like `NaN` and `-0`.

```js
NaN === NaN;       // false
Object.is(NaN, NaN); // true
0 === -0;          // true
Object.is(0, -0);  // false
```

---

## 7. Numbers and Precision

```js
0.1 + 0.2;                 // 0.30000000000000004
Number.isInteger(5);       // true
Number.parseFloat("3.14"); // 3.14
Number.parseInt("42px");   // 42
(1234.5678).toFixed(2);    // "1234.57"

// Safe integer range
Number.MAX_SAFE_INTEGER; // 9007199254740991
9007199254740992 === 9007199254740993; // true — precision lost
```

Use `BigInt` for integers beyond the safe range:

```js
const big = 9007199254740993n;
big + 1n; // 9007199254740994n
```

> **Gotcha:** Never compare floating-point currency directly. Store cents as integers or use a decimal library.

---

## 8. Control Flow Essentials

```js
// if / else if / else
if (score >= 90) {
  grade = "A";
} else if (score >= 80) {
  grade = "B";
} else {
  grade = "C";
}

// Ternary
const label = count === 1 ? "item" : "items";

// switch
switch (status) {
  case "loading":
    renderSpinner();
    break;
  case "error":
    renderError();
    break;
  default:
    renderContent();
}

// Nullish coalescing
const name = user.name ?? "Guest";      // only null/undefined fall back
const count = settings.count ?? 0;

// Optional chaining
const city = user?.address?.city;        // undefined instead of throwing

// Logical assignment
user.name ||= "Guest";
user.score ??= 0;
```

> **Gotcha:** `||` falls back on **any falsy** value, including `0` and `""`. Use `??` when `0` or `""` are valid.

---

## 9. Loops

```js
for (let i = 0; i < 3; i++) { ... }
for (const item of array) { ... }        // values
for (const key in object) { ... }        // keys (including inherited)
array.forEach((item, index) => { ... });

let i = 0;
while (i < 3) { i++; }

do { ... } while (condition);
```

- `for...of` works on iterables (arrays, strings, maps, sets).
- `for...in` is for enumerable property keys; avoid it on arrays.
- `break` exits the loop; `continue` skips to the next iteration.

```js
for (const [key, value] of Object.entries(object)) {
  console.log(key, value);
}
```

---

## 10. Error Handling Basics

```js
try {
  riskyOperation();
} catch (error) {
  console.error(error.message);
} finally {
  cleanup();
}
```

Error types you will see often:

| Error | Typical cause |
| :--- | :--- |
| `ReferenceError` | Using an undeclared variable |
| `TypeError` | Calling a non-function or reading a property of `null`/`undefined` |
| `SyntaxError` | Invalid code at parse time |
| `RangeError` | Invalid number range or call stack overflow |
| `DOMException` | Browser API failure |

```js
// Throw your own
function divide(a, b) {
  if (b === 0) throw new RangeError("Cannot divide by zero");
  return a / b;
}
```

> **Rule:** Catch only what you can handle. Do not silently swallow errors.

---

## ✅ Checkpoint

- [ ] What are the two phases of an execution context?
- [ ] Why does `console.log(a)` print `undefined` before `var a = 1`?
- [ ] What is the Temporal Dead Zone?
- [ ] Why is `typeof null === "object"`?
- [ ] When should you use `??` instead of `||`?
- [ ] Why is `0.1 + 0.2 !== 0.3`?
