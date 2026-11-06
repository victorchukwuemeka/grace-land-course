# Asynchronous JavaScript & the Event Loop

JavaScript runs on a single thread, yet it handles network requests, timers, and user input concurrently. The secret is the **event loop**, **task queues**, and **non-blocking APIs** provided by the host environment.

---

## 1. Synchronous vs Asynchronous

```js
// Synchronous: blocks until finished
console.log("start");
const result = heavyComputation(); // UI freezes
console.log(result);
console.log("end");

// Asynchronous: schedules work, continues immediately
console.log("start");
setTimeout(() => console.log("timer"), 0);
console.log("end");

// Output:
// start
// end
// timer
```

---

## 2. The Event Loop

```
┌───────────────────────────────┐
│          Call Stack           │
│  (runs one task at a time)    │
└───────────────┬───────────────┘
                │ empty?
                ▼
┌───────────────────────────────┐
│  Microtask queue              │
│  promise callbacks,           │
│  queueMicrotask, MutationObs. │
└───────────────┬───────────────┘
                │ drain ALL
                ▼
┌───────────────────────────────┐
│  Macrotask queue              │
│  setTimeout, setInterval,     │
│  I/O, UI events               │
└───────────────┬───────────────┘
                │ one task
                └──────────────▶ repeat
```

**Rules:**

1. Run the current synchronous code to completion.
2. Drain the **entire** microtask queue.
3. Run **one** macrotask.
4. Render if needed.
5. Repeat.

> **Critical:** Microtasks can starve the event loop. An infinite promise chain will prevent rendering and timers from running.

---

## 3. Predict the Output

```js
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

queueMicrotask(() => console.log("4"));

console.log("5");

// 1, 5, 3, 4, 2
```

```js
setTimeout(() => console.log("timeout"), 0);

Promise.resolve()
  .then(() => console.log("promise 1"))
  .then(() => console.log("promise 2"));

console.log("sync");

// sync, promise 1, promise 2, timeout
```

```js
async function run() {
  console.log("A");
  await Promise.resolve();
  console.log("B");
}

console.log("start");
run();
console.log("end");

// start, A, end, B
```

Everything after `await` is a microtask continuation.

---

## 4. Callbacks and Callback Hell

```js
getUser(id, (user) => {
  getOrders(user.id, (orders) => {
    getOrderDetails(orders[0].id, (details) => {
      render(details);
    });
  });
});
```

Problems: nested error handling, no return values, hard to compose.

**Solutions:** Promises, `async`/`await`, and named functions.

---

## 5. Promises

A promise represents a future value. It is in one of three states: **pending**, **fulfilled**, or **rejected**. Once settled, it cannot change.

```js
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const ok = true;
    ok ? resolve("done") : reject(new Error("failed"));
  }, 1000);
});

promise
  .then((value) => console.log(value))
  .catch((error) => console.error(error))
  .finally(() => console.log("cleanup"));
```

### Creating promises

```js
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

function loadImage(src) {
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.onload = () => resolve(img);
    img.onerror = () => reject(new Error(`Failed to load ${src}`));
    img.src = src;
  });
}
```

### Promise combinators

```js
// All succeed, or the first rejection wins
const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);

// All settle, never rejects
const results = await Promise.allSettled([fetchA(), fetchB()]);
// [{ status: "fulfilled", value }, { status: "rejected", reason }]

// First to settle wins
const fastest = await Promise.race([fetchA(), fetchB()]);

// First to fulfill wins
const firstOk = await Promise.any([fetchA(), fetchB()]);
```

| Combinator | Resolves when | Rejects when |
| :--- | :--- | :--- |
| `all` | All fulfill | Any rejects |
| `allSettled` | All settle | Never |
| `race` | First settles | First rejects |
| `any` | First fulfills | All reject |

> **Gotcha:** `Promise.all` fails fast. If one request fails, you lose the results of the others. Use `allSettled` when partial success is acceptable.

---

## 6. `async` / `await`

`async` functions always return a promise. `await` pauses the function (not the thread) until the promise settles.

```js
async function loadDashboard() {
  try {
    const [user, posts] = await Promise.all([
      fetchUser(),
      fetchPosts(),
    ]);
    render(user, posts);
  } catch (error) {
    showError(error);
  } finally {
    hideSpinner();
  }
}
```

### Sequential vs parallel

```js
// Sequential: total time = A + B
const user = await fetchUser();
const posts = await fetchPosts(user.id);

// Parallel: total time = max(A, B)
const [user, posts] = await Promise.all([
  fetchUser(),
  fetchPosts(),
]);
```

> **Important:** `await` inside a loop serializes requests. Map to promises first, then `Promise.all`.

```js
// Sequential (slow)
for (const id of ids) {
  results.push(await fetchItem(id));
}

// Parallel (fast)
const results = await Promise.all(ids.map((id) => fetchItem(id)));
```

### Error handling

```js
try {
  const data = await getData();
} catch (error) {
  if (error instanceof TypeError) {
    // network / parse issue
  } else {
    // application error
  }
}
```

A rejected promise that is never awaited becomes an **unhandled rejection**:

```js
window.addEventListener("unhandledrejection", (event) => {
  console.error("Unhandled:", event.reason);
});
```

---

## 7. Common Async Pitfalls

### Forgetting `await`

```js
async function save() {
  const result = saveToDb(); // missing await
  return result;            // returns a Promise, not the value
}
```

### `await` in `forEach`

```js
// Does not wait
items.forEach(async (item) => {
  await process(item);
});

// Correct
await Promise.all(items.map((item) => process(item)));
```

### Losing `this` in callbacks

```js
class Loader {
  constructor() { this.data = null; }

  async load() {
    const res = await fetch("/api");
    this.data = await res.json(); // arrow or async method preserves `this`
  }
}
```

### Zalgo (mixed sync/async behavior)

```js
// Bad: sometimes returns synchronously
function getValue(cache) {
  if (cache) return cache;
  return fetchValue();
}

// Good: always returns a promise
async function getValue(cache) {
  if (cache) return cache;
  return fetchValue();
}
```

---

## 8. Timeouts and Cancellation

```js
function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) => {
    setTimeout(() => reject(new Error("Timed out")), ms);
  });
  return Promise.race([promise, timeout]);
}
```

Use `AbortController` for cancellable operations (see the Fetch module).

---

## 9. Web Workers

For CPU-heavy work, move it off the main thread.

```js
// main.js
const worker = new Worker("worker.js", { type: "module" });
worker.postMessage({ numbers: largeArray });
worker.onmessage = (event) => console.log(event.data);

// worker.js
self.onmessage = (event) => {
  const result = event.data.numbers.sort((a, b) => a - b);
  self.postMessage(result);
};
```

Workers cannot access the DOM. They communicate via `postMessage` and structured cloning.

---

## 10. `requestAnimationFrame`

Use it for visual updates synchronized with the display refresh.

```js
function animate(time) {
  element.style.transform = `translateX(${Math.min(time / 10, 300)}px)`;
  if (time / 10 < 300) requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

> **Note:** `setInterval` is not synchronized with rendering and can cause jank. Prefer `requestAnimationFrame` for animations.

---

## 11. Async Checklist

- [ ] Independent requests run in parallel.
- [ ] Every promise has a `catch` or `try/catch`.
- [ ] `await` is used inside loops only when order matters.
- [ ] Loading, error, and empty states are handled.
- [ ] Long-running requests are cancellable.
- [ ] CPU-heavy work is moved to a worker.
- [ ] No unhandled rejections in the console.

---

## ✅ Checkpoint

- [ ] What is the difference between a microtask and a macrotask?
- [ ] Why does `Promise.resolve().then()` run before `setTimeout(..., 0)`?
- [ ] What does `async` actually return?
- [ ] Why does `forEach(async ...)` not await?
- [ ] When should you use `Promise.allSettled`?
- [ ] What happens to code after `await`?
