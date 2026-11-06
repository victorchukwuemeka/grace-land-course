# Module 04: JavaScript Deep Dive (ES6+)

JavaScript is the programming language of the web. This module goes beyond syntax: you will learn how the engine executes code, how scope and closures work, how objects and prototypes model behavior, and how asynchronous code actually runs.

> **Core idea:** JavaScript is single-threaded, but its runtime is not. The call stack, task queues, and event loop let one thread handle timers, network requests, and user input without blocking the UI.

---

## 📚 Module Contents

| File | What you will learn |
| :--- | :--- |
| [`01-js-fundamentals-and-execution.md`](./01-js-fundamentals-and-execution.md) | Engine, execution context, call stack, types, coercion, equality, `var`/`let`/`const`, hoisting. |
| [`02-functions-scope-closures.md`](./02-functions-scope-closures.md) | Function forms, scope chain, closures, `this`, `call`/`apply`/`bind`, currying. |
| [`03-objects-prototypes-classes.md`](./03-objects-prototypes-classes.md) | Object creation, prototypes, classes, inheritance, private fields, immutability. |
| [`04-es6-plus-modern-syntax.md`](./04-es6-plus-modern-syntax.md) | Destructuring, spread/rest, modules, iterators, generators, `Map`/`Set`, array methods. |
| [`05-dom-manipulation-and-events.md`](./05-dom-manipulation-and-events.md) | Selection, creation, events, delegation, forms, accessibility. |
| [`06-async-javascript-event-loop.md`](./06-async-javascript-event-loop.md) | Event loop, microtasks/macrotasks, promises, `async`/`await`, error handling, concurrency. |
| [`07-fetch-api-and-http.md`](./07-fetch-api-and-http.md) | HTTP basics, `fetch`, REST, `AbortController`, CORS, authentication patterns. |
| [`08-storage-and-browser-apis.md`](./08-storage-and-browser-apis.md) | `localStorage`, cookies, IndexedDB, Clipboard, Geolocation, Notifications. |
| [`09-modules-tooling-and-best-practices.md`](./09-modules-tooling-and-best-practices.md) | ES modules, bundlers, linting, testing, security, performance. |
| [`exercises/js-lab.md`](./exercises/js-lab.md) | 15 coding challenges with acceptance criteria. |

---

## 🎯 Learning Outcomes

By the end of this module you should be able to:

1. Explain what happens when JavaScript runs a line of code.
2. Use closures intentionally, not accidentally.
3. Model data and behavior with objects, prototypes, and classes.
4. Manipulate the DOM efficiently and handle events correctly.
5. Predict the output order of `setTimeout`, promises, and `await`.
6. Fetch data from an API and handle loading, error, and empty states.
7. Persist state in the browser appropriately.
8. Write modular, testable, secure client-side code.

---

## 🛠️ Running the Examples

Create `index.html` and `app.js`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JavaScript Lab</title>
</head>
<body>
  <script type="module" src="app.js"></script>
</body>
</html>
```

```js
// app.js
console.log("JavaScript is running");
```

Open the page, then use **DevTools → Console**. `type="module"` gives you strict mode and `import`/`export` support.

> **Tip:** For quick experiments, the DevTools Console and [JSBin](https://jsbin.com) / [CodePen](https://codepen.io) are enough. For projects, use VS Code with Live Server.

---

## 🧭 How to Study This Module

- **Type every example.** Do not copy-paste.
- **Predict before running.** Write down what you think will happen, then run it.
- **Break things.** Change a `const` to a `let`, remove an `await`, mutate a frozen object.
- **Use the debugger.** Set breakpoints in the Sources panel and step through the call stack.
- **Read error messages.** They usually tell you the file, line, and cause.
