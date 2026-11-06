# DOM Manipulation & Events

The **Document Object Model (DOM)** is an in-memory tree of nodes representing the page. JavaScript can read and mutate that tree, and the browser re-renders. Efficient DOM work means batching reads and writes, minimizing reflows, and using event delegation.

---

## 1. The DOM Tree

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Page</title>
  </head>
  <body>
    <main id="app">
      <h1 class="title">Hello</h1>
      <ul>
        <li>One</li>
        <li>Two</li>
      </ul>
    </main>
  </body>
</html>
```

```
document
└── html
    ├── head
    │   └── title
    └── body
        └── main#app
            ├── h1.title
            └── ul
                ├── li
                └── li
```

Node types: `Element`, `Text`, `Comment`, `Document`, `DocumentFragment`.

---

## 2. Selecting Elements

```js
document.getElementById("app");              // fastest, single
document.querySelector(".title");            // first match
document.querySelectorAll(".card");          // static NodeList
document.getElementsByClassName("card");     // live HTMLCollection
document.getElementsByTagName("li");         // live HTMLCollection
```

| Method | Returns | Live? |
| :--- | :--- | :--- |
| `querySelector` | `Element` or `null` | — |
| `querySelectorAll` | static `NodeList` | No |
| `getElementsByClassName` | `HTMLCollection` | Yes |
| `getElementById` | `Element` or `null` | — |

```js
// NodeList is iterable in modern browsers
document.querySelectorAll(".card").forEach((card) => {
  card.classList.add("is-visible");
});

// Convert to array for array methods
const cards = [...document.querySelectorAll(".card")];
```

> **Gotcha:** Live collections update automatically. If you remove elements while iterating, the collection changes underneath you.

### Traversal

```js
const el = document.querySelector(".card");

el.parentElement;
el.children;              // element children only
el.childNodes;            // all nodes, including text
el.firstElementChild;
el.lastElementChild;
el.nextElementSibling;
el.previousElementSibling;
el.closest(".container"); // nearest ancestor matching selector
el.matches(".card--featured");
```

---

## 3. Reading and Writing Content

```js
const el = document.querySelector(".title");

el.textContent;              // text only, safe
el.innerHTML;                // parsed HTML, dangerous with user input
el.innerText;                // rendered text (forces layout, slower)

el.textContent = "New title";
el.innerHTML = "<strong>Bold</strong>";
```

### `innerHTML` vs `textContent` vs DOM methods

```js
// Safe for plain text
el.textContent = userInput;

// Dangerous: XSS if userInput is untrusted
el.innerHTML = `<p>${userInput}</p>`;

// Safe construction
const p = document.createElement("p");
p.textContent = userInput;
el.append(p);
```

> **Security:** `innerHTML` executes inline handlers and can load external resources. Sanitize with a library such as DOMPurify if you must render HTML.

### Attributes vs properties

```js
const input = document.querySelector("input");

input.getAttribute("value"); // initial HTML attribute
input.value;                 // current live property

input.setAttribute("aria-label", "Search");
input.dataset.userId = "42"; // data-user-id="42"
input.removeAttribute("disabled");
input.hasAttribute("required");
```

| Attribute | Property |
| :--- | :--- |
| Initial HTML value | Current runtime value |
| String | May be typed (boolean, number) |
| `class` | `className` / `classList` |

---

## 4. Classes and Styles

```js
el.classList.add("active");
el.classList.remove("hidden");
el.classList.toggle("open", isOpen);
el.classList.replace("old", "new");
el.classList.contains("active");

// Inline styles (prefer classes for state)
el.style.backgroundColor = "red";
el.style.setProperty("--progress", "50%");
el.style.cssText = "color: red; font-weight: bold;";
```

> **Best practice:** Use classes for styling and state. Inline styles are for dynamic values that cannot be expressed as classes (e.g., drag position, progress width).

---

## 5. Creating and Inserting Nodes

```js
const card = document.createElement("article");
card.className = "card";
card.textContent = "New card";

container.append(card);        // end
container.prepend(card);       // start
card.before(other);            // before card
card.after(other);             // after card
card.replaceWith(newCard);
card.remove();

// HTML string parsing
container.insertAdjacentHTML("beforeend", "<p>Hello</p>");
```

### `DocumentFragment` for batch inserts

```js
const fragment = document.createDocumentFragment();

items.forEach((item) => {
  const li = document.createElement("li");
  li.textContent = item.name;
  fragment.append(li);
});

list.append(fragment); // one DOM insertion, one reflow
```

> **Performance:** Every DOM mutation can trigger style recalculation and layout. Batch changes with fragments, or build an HTML string and assign once (for trusted content).

---

## 6. Events

```js
const button = document.querySelector("button");

button.addEventListener("click", (event) => {
  console.log(event.type, event.target);
});

// Options
button.addEventListener("click", handler, { once: true });
button.addEventListener("scroll", handler, { passive: true });
button.addEventListener("click", handler, { capture: true });

// Remove (same function reference required)
button.removeEventListener("click", handler);
```

### The event object

```js
element.addEventListener("click", (event) => {
  event.target;          // element that triggered the event
  event.currentTarget;   // element the listener is attached to
  event.preventDefault();// stop default browser behavior
  event.stopPropagation();// stop bubbling
  event.key;             // keyboard events
  event.clientX;         // mouse position
});
```

> **Gotcha:** `event.target` is the innermost element clicked. `event.currentTarget` is the element whose listener is running. They differ when events bubble.

---

## 7. Event Flow: Capturing, Targeting, Bubbling

```
Capture phase ──▶ Target ──▶ Bubble phase
document ─▶ ... ─▶ button ─▶ ... ─▶ document
```

```js
// Capture: runs on the way down
document.addEventListener("click", handler, true);

// Bubble (default): runs on the way up
document.addEventListener("click", handler, false);
```

Some events do not bubble: `focus`, `blur`, `mouseenter`, `mouseleave`, `load`. Use `focusin`/`focusout` when you need bubbling.

---

## 8. Event Delegation

Attach one listener to a common ancestor instead of one per child.

```js
const list = document.querySelector(".todo-list");

list.addEventListener("click", (event) => {
  const button = event.target.closest("[data-action]");
  if (!button || !list.contains(button)) return;

  const item = button.closest(".todo-item");
  const action = button.dataset.action;

  if (action === "delete") item.remove();
  if (action === "toggle") item.classList.toggle("is-done");
});
```

**Benefits:** fewer listeners, works for dynamically added elements, less memory.

> **Gotcha:** Always verify the target is inside the intended container (`list.contains(button)`). Otherwise a nested component with the same data attribute can trigger the wrong action.

---

## 9. Forms

```html
<form id="signup" novalidate>
  <label for="email">Email</label>
  <input id="email" name="email" type="email" required />
  <span class="error" id="email-error" aria-live="polite"></span>

  <button type="submit">Sign up</button>
</form>
```

```js
const form = document.querySelector("#signup");

form.addEventListener("submit", (event) => {
  event.preventDefault();

  const data = new FormData(form);
  const email = data.get("email");

  if (!form.checkValidity()) {
    form.reportValidity();
    return;
  }

  console.log(Object.fromEntries(data));
});
```

### Validation

```js
input.validity.valid;
input.validity.typeMismatch;
input.validationMessage;

input.setCustomValidity("Enter a company email");
input.setCustomValidity(""); // clear
```

> **Accessibility:** Do not rely on color alone for errors. Associate messages with `aria-describedby`, use `aria-invalid`, and announce changes with `aria-live`.

---

## 10. Accessibility in the DOM

```js
// Prefer semantic elements; use ARIA only when necessary
button.setAttribute("aria-expanded", String(isOpen));
dialog.setAttribute("aria-modal", "true");
dialog.setAttribute("role", "dialog");
dialog.setAttribute("aria-labelledby", "dialog-title");
```

Rules:
1. Use `<button>` for actions and `<a>` for navigation.
2. Every form control needs a `<label>`.
3. Manage focus when opening/closing dialogs.
4. Keep the DOM order logical.
5. Do not remove focus outlines.

```js
// Move focus to a heading after navigation
heading.setAttribute("tabindex", "-1");
heading.focus();
```

---

## 11. Performance: Read/Write Batching

```js
// Bad: forces layout on every iteration
items.forEach((item) => {
  const height = item.offsetHeight; // read (layout)
  item.style.height = `${height * 2}px`; // write (invalidate)
});

// Good: batch reads, then batch writes
const heights = items.map((item) => item.offsetHeight);
items.forEach((item, i) => {
  item.style.height = `${heights[i] * 2}px`;
});
```

Use `requestAnimationFrame` for visual updates:

```js
requestAnimationFrame(() => {
  element.style.transform = `translateX(${x}px)`;
});
```

Use `IntersectionObserver` instead of scroll listeners for visibility:

```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      entry.target.classList.add("is-visible");
      observer.unobserve(entry.target);
    }
  });
}, { threshold: 0.1 });

document.querySelectorAll(".reveal").forEach((el) => observer.observe(el));
```

---

## 12. DOM Checklist

- [ ] Elements are selected once and cached.
- [ ] No `innerHTML` with untrusted data.
- [ ] Event listeners use delegation where appropriate.
- [ ] Removed elements have their listeners cleaned up.
- [ ] Forms have labels and accessible error messages.
- [ ] Focus is managed for modals and route changes.
- [ ] Animations use `transform`/`opacity`.
- [ ] No layout thrashing in loops.

---

## ✅ Checkpoint

- [ ] What is the difference between `textContent` and `innerHTML`?
- [ ] Why does `event.target` differ from `event.currentTarget`?
- [ ] How does event delegation work?
- [ ] Why is `DocumentFragment` faster for many inserts?
- [ ] What causes layout thrashing?
- [ ] When would you use `IntersectionObserver` instead of a scroll event?
