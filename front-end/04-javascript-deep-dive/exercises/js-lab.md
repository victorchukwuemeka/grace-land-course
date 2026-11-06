# JavaScript Lab: 15 Coding Challenges

Work in `js-lab/` with one file per challenge or a single `app.js` with sections. For each challenge:

1. Write the code yourself.
2. Test edge cases.
3. Compare against the acceptance criteria.
4. Refactor for readability.

---

## Challenge 1: FizzBuzz (warm-up)

Print numbers 1–100. For multiples of 3 print `Fizz`, multiples of 5 print `Buzz`, multiples of both print `FizzBuzz`.

**Acceptance criteria:** No `if/else` chain longer than necessary; handles 1–100 correctly.

---

## Challenge 2: Reverse a String Without `.reverse()`

```js
reverse("hello"); // "olleh"
```

**Acceptance criteria:** Handles empty strings, unicode code points, and does not mutate the input.

---

## Challenge 3: Palindrome Checker

```js
isPalindrome("A man, a plan, a canal: Panama"); // true
isPalindrome("race a car");                     // false
```

**Acceptance criteria:** Ignores case, spaces, and punctuation. O(n) time.

---

## Challenge 4: Unique Values

```js
unique([1, 2, 2, 3, 1]); // [1, 2, 3]
```

**Acceptance criteria:** Preserves first occurrence order. Do it once with `Set` and once with a loop.

---

## Challenge 5: Flatten an Array

```js
flatten([1, [2, [3, [4]]]]); // [1, 2, 3, 4]
```

**Acceptance criteria:** Works to arbitrary depth. Do it recursively and iteratively.

---

## Challenge 6: Debounce

Implement `debounce(fn, delay)` so `fn` runs only after calls stop for `delay` ms.

```js
const search = debounce((q) => console.log(q), 300);
search("c"); search("cs"); search("css"); // only "css" logs after 300ms
```

**Acceptance criteria:** Preserves `this` and arguments. Returns a function with a `cancel()` method.

---

## Challenge 7: Throttle

Implement `throttle(fn, limit)` so `fn` runs at most once per `limit` ms.

**Acceptance criteria:** First call runs immediately. Trailing call is handled correctly.

---

## Challenge 8: Deep Clone

```js
const copy = deepClone({ a: 1, b: { c: [1, 2] }, d: new Date() });
```

**Acceptance criteria:** Handles nested objects, arrays, and `Date`. Explain why `JSON.parse(JSON.stringify())` is insufficient.

---

## Challenge 9: Group By

```js
groupBy(users, (u) => u.role);
// { admin: [...], member: [...] }
```

**Acceptance criteria:** Does not mutate the input. Handles keys that collide with object prototype names (`__proto__`, `constructor`) — use a `Map` or `Object.create(null)`.

---

## Challenge 10: Event Delegation Todo List

Build a todo list with:

- Add item
- Toggle complete
- Delete item
- Persist to `localStorage`
- One delegated click listener

**Acceptance criteria:**
- Items survive a page reload.
- Uses `data-id` attributes.
- No `innerHTML` with user input.
- Keyboard accessible.

---

## Challenge 11: Fetch Dashboard

Fetch users and posts from `https://jsonplaceholder.typicode.com`.

**Tasks:**
1. Show a loading state.
2. Show an error state with a retry button.
3. Show an empty state if there are no results.
4. Cancel the request when the user navigates away.

**Acceptance criteria:** Uses `Promise.all`, `AbortController`, and `response.ok` checks.

---

## Challenge 12: Search with Debounce + Cancellation

Build a search input that queries an API as the user types.

**Acceptance criteria:**
- Debounced at 300ms.
- Previous request aborted.
- Loading spinner while pending.
- Stale responses never overwrite newer ones.

---

## Challenge 13: Predict the Output

Without running the code, write the output order. Then verify.

```js
console.log("A");

setTimeout(() => console.log("B"), 0);

Promise.resolve()
  .then(() => console.log("C"))
  .then(() => console.log("D"));

(async () => {
  console.log("E");
  await null;
  console.log("F");
})();

console.log("G");
```

**Acceptance criteria:** You can explain each line using the call stack, microtask queue, and macrotask queue.

---

## Challenge 14: Memoize

```js
const slowSquare = (n) => {
  console.log("computing");
  return n * n;
};

const fastSquare = memoize(slowSquare);
fastSquare(4); // "computing" → 16
fastSquare(4); // 16 (cached)
```

**Acceptance criteria:** Works with multiple arguments. Exposes a `cache` and a `clear()` method. Handles `undefined` and `NaN` arguments sensibly.

---

## Challenge 15: Build a Mini Router

Create a client-side router using the History API.

**Requirements:**
- Routes: `/`, `/about`, `/contact`
- Intercept internal link clicks.
- Handle back/forward (`popstate`).
- Render a 404 for unknown routes.
- Update the document title.
- Move focus to the main heading after navigation.

**Acceptance criteria:**
- No full page reload.
- Direct URL visits work (dev server fallback).
- Accessible focus management.

---

## Stretch Projects

### 1. Weather Dashboard

- Search by city.
- Show current conditions and a 5-day forecast.
- Cache the last result in `localStorage`.
- Handle offline and API errors.
- Use `AbortController` for rapid searches.

### 2. Kanban Board

- Columns: Backlog, In Progress, Done.
- Drag and drop between columns.
- Persist state in `localStorage`.
- Keyboard alternative for moving cards.
- Accessible announcements for moves.

### 3. Quiz App

- Questions loaded from a JSON file.
- Timer per question.
- Score tracking.
- Review answers after completion.
- Persist high score.

---

## Self-Assessment Rubric

| Level | Description |
| :--- | :--- |
| **Beginner** | Code works for the happy path. |
| **Intermediate** | Handles errors and edge cases; functions are small and named well. |
| **Advanced** | Tests included, accessible UI, no memory leaks, documented trade-offs. |
| **Professional** | Modular architecture, typed or validated data, performance measured, security considered. |
