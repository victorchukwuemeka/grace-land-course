# Functions, Scope, Closures & `this`

Functions are the unit of composition in JavaScript. They are also objects, they carry scope, and they determine what `this` means at call time.

---

## 1. Function Forms

```js
// Function declaration (hoisted)
function greet(name) {
  return `Hello, ${name}`;
}

// Function expression (not hoisted)
const greet2 = function (name) {
  return `Hello, ${name}`;
};

// Arrow function (no own `this`, `arguments`, or `prototype`)
const greet3 = (name) => `Hello, ${name}`;

// Method shorthand
const user = {
  name: "Ada",
  greet() { return `Hello, ${this.name}`; },
};

// Immediately Invoked Function Expression (legacy scoping)
(function () {
  console.log("runs immediately");
})();
```

### Arrow functions vs regular functions

| Feature | Regular function | Arrow function |
| :--- | :--- | :--- |
| Own `this` | Yes (dynamic) | No (lexical) |
| `arguments` object | Yes | No |
| Can be a constructor (`new`) | Yes | No |
| `prototype` property | Yes | No |
| Hoisted (declaration) | Yes | No |

```js
const counter = {
  count: 0,
  start() {
    setInterval(() => {
      this.count++; // arrow inherits `this` from start()
      console.log(this.count);
    }, 1000);
  },
};
```

> **Rule:** Use arrow functions for callbacks that should inherit `this`. Use regular functions for object methods, constructors, and anything that needs `arguments`.

---

## 2. Parameters

```js
// Default parameters
function createUser(name, role = "member") {
  return { name, role };
}

// Rest parameters (always last)
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}

// Destructured parameters
function render({ title, body, tags = [] }) {
  return `${title}: ${body} (${tags.join(", ")})`;
}
```

> **Gotcha:** Default values are evaluated at call time, so mutable defaults can leak state:

```js
function addItem(item, list = []) {
  list.push(item); // a new array is created per call because the default runs each time
  return list;
}
```

---

## 3. Scope

JavaScript has four kinds of scope:

1. **Global** — accessible everywhere.
2. **Module** — top-level bindings in an ES module (not global).
3. **Function** — `var` and parameters.
4. **Block** — `let`, `const`, and function declarations inside `{}`.

```js
const global = "global";

function outer() {
  const outerVar = "outer";

  function inner() {
    const innerVar = "inner";
    console.log(global, outerVar, innerVar); // all accessible
  }

  inner();
  // console.log(innerVar); // ReferenceError
}
```

### Scope chain

When a variable is referenced, the engine looks in the current scope, then the outer scope, then the next outer scope, up to the global scope. This chain is determined **lexically** (where the function is written), not dynamically (where it is called).

### Shadowing

```js
const name = "global";

function greet() {
  const name = "local"; // shadows the global
  console.log(name);    // "local"
}
```

> **Avoid:** Global variables. They create hidden coupling and name collisions. Modules and closures keep state contained.

---

## 4. Closures

A **closure** is a function bundled with its lexical environment. It can access variables from its outer scope even after the outer function has returned.

```js
function createCounter() {
  let count = 0;

  return {
    increment() { count++; return count; },
    decrement() { count--; return count; },
    value() { return count; },
  };
}

const counter = createCounter();
counter.increment(); // 1
counter.increment(); // 2
counter.value();     // 2
```

`count` is private. Nothing outside `createCounter` can modify it directly.

### Why closures matter

- **Data privacy** — the module pattern.
- **Function factories** — pre-configured functions.
- **Event handlers** — remembering state.
- **Debounce/throttle** — remembering timers.
- **Memoization** — caching results.

```js
function once(fn) {
  let called = false;
  let result;

  return function (...args) {
    if (!called) {
      called = true;
      result = fn.apply(this, args);
    }
    return result;
  };
}
```

### The classic loop bug

```js
// Bug: all callbacks log 3
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}

// Fix 1: block scope
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}

// Fix 2: closure factory (ES5 style)
for (var j = 0; j < 3; j++) {
  (function (value) {
    setTimeout(() => console.log(value), 0);
  })(j);
}
```

> **Memory note:** Closures keep referenced variables alive. A large object captured by a long-lived handler cannot be garbage-collected until the handler is released.

---

## 5. `this` Binding Rules

`this` is determined by **how a function is called**, not where it is defined (except arrows).

```js
// 1. Global / undefined (strict mode)
function show() { console.log(this); }
show(); // undefined in modules/strict mode

// 2. Method call
const obj = { name: "Ada", show() { console.log(this.name); } };
obj.show(); // "Ada"

// 3. Explicit binding
function greet() { console.log(this.name); }
greet.call({ name: "Grace" });  // "Grace"
greet.apply({ name: "Lin" });   // "Lin"
const bound = greet.bind({ name: "Alan" });
bound();                        // "Alan"

// 4. Constructor call
function Person(name) { this.name = name; }
const ada = new Person("Ada");

// 5. Arrow functions inherit `this`
const team = {
  name: "Core",
  members: ["A", "B"],
  list() {
    return this.members.map((m) => `${this.name}: ${m}`);
  },
};
```

### Lost `this`

```js
const user = {
  name: "Ada",
  greet() { console.log(this.name); },
};

const fn = user.greet;
fn(); // TypeError or undefined — `this` is lost

const safe = user.greet.bind(user);
safe(); // "Ada"
```

> **Gotcha:** Passing `obj.method` as a callback loses its receiver. Bind it or wrap it in an arrow.

---

## 6. `call`, `apply`, `bind`

```js
function introduce(greeting, punctuation) {
  return `${greeting}, ${this.name}${punctuation}`;
}

introduce.call({ name: "Ada" }, "Hello", "!");   // "Hello, Ada!"
introduce.apply({ name: "Ada" }, ["Hi", "."]);  // "Hi, Ada."
const helloAda = introduce.bind({ name: "Ada" });
helloAda("Hey", "!");                           // "Hey, Ada!"
```

| Method | Arguments | Returns |
| :--- | :--- | :--- |
| `call` | Comma-separated | Function result |
| `apply` | Array | Function result |
| `bind` | Comma-separated | New bound function |

---

## 7. Higher-Order Functions

A higher-order function takes or returns another function.

```js
function multiplyBy(factor) {
  return (number) => number * factor;
}

const double = multiplyBy(2);
double(5); // 10

// Composition
const pipe = (...fns) => (value) =>
  fns.reduce((acc, fn) => fn(acc), value);

const process = pipe(
  (s) => s.trim(),
  (s) => s.toLowerCase(),
  (s) => s.replace(/\s+/g, "-")
);

process("  Hello World  "); // "hello-world"
```

### Currying

```js
const curry = (fn) => {
  const arity = fn.length;
  return function curried(...args) {
    return args.length >= arity
      ? fn(...args)
      : (...next) => curried(...args, ...next);
  };
};

const add = curry((a, b, c) => a + b + c);
add(1)(2)(3); // 6
add(1, 2)(3); // 6
```

### Debounce and throttle

```js
function debounce(fn, delay = 300) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

function throttle(fn, limit = 300) {
  let waiting = false;
  return function (...args) {
    if (!waiting) {
      fn.apply(this, args);
      waiting = true;
      setTimeout(() => { waiting = false; }, limit);
    }
  };
}
```

- **Debounce:** run after activity stops (search input).
- **Throttle:** run at most once per interval (scroll handler).

---

## 8. Pure Functions and Side Effects

A **pure function**:

1. Returns the same output for the same input.
2. Has no observable side effects.

```js
// Pure
function add(a, b) { return a + b; }

// Impure: mutates external state
let total = 0;
function addToTotal(n) { total += n; }

// Impure: depends on external state/time
function timestamp() { return Date.now(); }
```

Pure functions are easier to test, reason about, and memoize. Push side effects (DOM, network, storage) to the edges of your application.

---

## ✅ Checkpoint

- [ ] What is the difference between function scope and block scope?
- [ ] How does a closure keep a variable alive?
- [ ] Why does `let` fix the `setTimeout` loop bug?
- [ ] What are the five `this` binding rules?
- [ ] When would you use `bind` instead of an arrow function?
- [ ] What is the difference between debounce and throttle?
