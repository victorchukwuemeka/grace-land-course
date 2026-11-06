# Browser Storage & Platform APIs

Modern browsers are application platforms. Beyond the DOM, they provide storage, device access, and background capabilities. Use them deliberately: each API has privacy, security, and performance implications.

---

## 1. Choosing a Storage Mechanism

| API | Capacity | Persistence | Sync/Async | Accessible to JS? |
| :--- | :--- | :--- | :--- | :--- |
| `localStorage` | ~5–10 MB | Until cleared | Sync | Yes |
| `sessionStorage` | ~5 MB | Tab lifetime | Sync | Yes |
| Cookies | ~4 KB each | Configurable expiry | Sync (header) | Only if not `HttpOnly` |
| IndexedDB | Large (hundreds of MB+) | Until cleared | Async | Yes |
| Cache Storage | Large | Until cleared | Async | Yes |
| Memory (variables) | RAM | Page lifetime | Sync | Yes |

> **Rule of thumb:** Preferences and small non-sensitive state → `localStorage`. Per-tab state → `sessionStorage`. Sessions/auth → `HttpOnly` cookies. Large structured data / offline assets → IndexedDB or Cache Storage.

---

## 2. `localStorage`

```js
localStorage.setItem("theme", "dark");
localStorage.getItem("theme");       // "dark"
localStorage.removeItem("theme");
localStorage.clear();                // removes everything for the origin
localStorage.key(0);                 // key at index
localStorage.length;
```

Values are always **strings**:

```js
// Store objects
localStorage.setItem("user", JSON.stringify({ name: "Ada" }));

// Read safely
function readJSON(key, fallback = null) {
  try {
    const raw = localStorage.getItem(key);
    return raw === null ? fallback : JSON.parse(raw);
  } catch {
    return fallback;
  }
}

const user = readJSON("user", {});
```

> **Gotchas:**
> - Synchronous — large writes block the main thread.
> - Available only in secure, same-origin contexts.
> - Shared across tabs of the same origin.
> - Users can clear it at any time; always handle missing data.
> - Private browsing quotas may be zero.

### Cross-tab synchronization

```js
window.addEventListener("storage", (event) => {
  if (event.key === "theme") {
    applyTheme(event.newValue);
  }
});
```

The `storage` event fires in **other** tabs, not the one that made the change.

---

## 3. `sessionStorage`

Same API as `localStorage`, but scoped to a single tab and cleared when the tab closes.

```js
sessionStorage.setItem("draft", text);
```

Use for multi-step form drafts, one-time onboarding state, or per-tab filters.

---

## 4. Cookies

```js
// Client-side (avoid for sensitive data)
document.cookie = "theme=dark; path=/; max-age=31536000; SameSite=Lax";

// Read all cookies
document.cookie; // "theme=dark; other=value"
```

### Cookie attributes

| Attribute | Purpose |
| :--- | :--- |
| `Secure` | Sent only over HTTPS |
| `HttpOnly` | Not accessible to JavaScript (server-set) |
| `SameSite=Strict` | Not sent on cross-site requests |
| `SameSite=Lax` | Sent on top-level navigations (default) |
| `SameSite=None` | Sent cross-site; requires `Secure` |
| `Max-Age` / `Expires` | Lifetime |
| `Path` / `Domain` | Scope |

> **Security:** Session tokens should be `HttpOnly; Secure; SameSite=Lax` cookies set by the server. This prevents JavaScript (and therefore most XSS) from reading them.

---

## 5. IndexedDB

IndexedDB is a transactional, asynchronous, object-oriented database in the browser.

```js
function openDB() {
  return new Promise((resolve, reject) => {
    const request = indexedDB.open("app-db", 1);

    request.onupgradeneeded = () => {
      const db = request.result;
      if (!db.objectStoreNames.contains("notes")) {
        const store = db.createObjectStore("notes", { keyPath: "id" });
        store.createIndex("byUpdated", "updatedAt");
      }
    };

    request.onsuccess = () => resolve(request.result);
    request.onerror = () => reject(request.error);
  });
}

async function addNote(note) {
  const db = await openDB();
  return new Promise((resolve, reject) => {
    const tx = db.transaction("notes", "readwrite");
    tx.objectStore("notes").put(note);
    tx.oncomplete = () => resolve();
    tx.onerror = () => reject(tx.error);
  });
}

async function getAllNotes() {
  const db = await openDB();
  return new Promise((resolve, reject) => {
    const tx = db.transaction("notes", "readonly");
    const request = tx.objectStore("notes").getAll();
    request.onsuccess = () => resolve(request.result);
    request.onerror = () => reject(request.error);
  });
}
```

> **Simpler alternative:** Use the `idb` library (a tiny promise wrapper) or `localForage` for key-value storage. For complex offline apps, consider the Cache API + a service worker.

### When to use IndexedDB

- Large datasets (thousands of records).
- Binary data (images, files).
- Offline-first apps.
- Data that must be queried by index.

---

## 6. Cache Storage & Service Workers

```js
// Cache assets for offline use
const cache = await caches.open("static-v1");
await cache.addAll(["/", "/styles.css", "/app.js", "/offline.html"]);

// Respond from cache
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((cached) => cached || fetch(event.request))
  );
});
```

> **Note:** Service workers require HTTPS (or `localhost`) and run on a separate thread. They enable offline support, background sync, and push notifications.

---

## 7. Clipboard API

```js
await navigator.clipboard.writeText("Copied text");

const text = await navigator.clipboard.readText();
```

Requires a secure context and, in many browsers, a user gesture. Handle permission errors:

```js
try {
  await navigator.clipboard.writeText(code);
  showToast("Copied!");
} catch {
  showToast("Copy failed — select and copy manually.");
}
```

> **Fallback:** For legacy browsers, use a hidden `<textarea>` + `document.execCommand("copy")` (deprecated).

---

## 8. Geolocation

```js
navigator.geolocation.getCurrentPosition(
  (position) => {
    const { latitude, longitude } = position.coords;
    console.log(latitude, longitude);
  },
  (error) => {
    switch (error.code) {
      case error.PERMISSION_DENIED:
        console.log("User denied location");
        break;
      case error.POSITION_UNAVAILABLE:
        console.log("Location unavailable");
        break;
      case error.TIMEOUT:
        console.log("Location request timed out");
        break;
    }
  },
  { enableHighAccuracy: false, timeout: 10_000, maximumAge: 60_000 }
);
```

> **Privacy:** Always explain why you need location and provide a manual alternative. Never request it on page load without context.

---

## 9. Notifications

```js
const permission = await Notification.requestPermission();

if (permission === "granted") {
  new Notification("Build complete", {
    body: "Your project compiled successfully.",
    icon: "/icon.png",
  });
}
```

For push notifications, you need a service worker and a push subscription. Never request permission on first load; ask after a clear user action.

---

## 10. Other Useful APIs

| API | Use case |
| :--- | :--- |
| `Intl` | Dates, numbers, currency, relative time, collation |
| `matchMedia` | Respond to media queries in JS |
| `ResizeObserver` | React to element size changes |
| `IntersectionObserver` | Lazy loading, infinite scroll, visibility |
| `MutationObserver` | React to DOM changes |
| `PerformanceObserver` | Measure paint, layout shift, long tasks |
| `URL` / `URLSearchParams` | Parse and build URLs |
| `BroadcastChannel` | Cross-tab messaging |
| `Web Share` | Native share sheet |
| `Screen Wake Lock` | Keep the screen on |
| `Payment Request` | Native payment flows |

### `Intl` examples

```js
new Intl.NumberFormat("en-US", { style: "currency", currency: "USD" })
  .format(1234.5); // "$1,234.50"

new Intl.DateTimeFormat("en-GB", { dateStyle: "long" })
  .format(new Date()); // "6 November 2026"

new Intl.RelativeTimeFormat("en", { numeric: "auto" })
  .format(-1, "day"); // "yesterday"
```

### `BroadcastChannel`

```js
const channel = new BroadcastChannel("app-events");
channel.postMessage({ type: "logout" });
channel.onmessage = (event) => console.log(event.data);
```

---

## 11. Storage Best Practices

- **Never store secrets** in `localStorage` or `sessionStorage`.
- **Version your keys** (`app:v1:settings`) so migrations are explicit.
- **Handle quota errors**:

```js
try {
  localStorage.setItem(key, value);
} catch (error) {
  if (error.name === "QuotaExceededError") {
    pruneOldData();
  }
}
```

- **Encrypt nothing client-side and call it secure** — the key is also on the client.
- **Expire stale data** with a timestamp.
- **Ask before persisting personal data** and provide a clear reset option.

---

## ✅ Checkpoint

- [ ] What is the difference between `localStorage` and `sessionStorage`?
- [ ] Why should auth tokens live in `HttpOnly` cookies?
- [ ] When is IndexedDB a better choice than `localStorage`?
- [ ] What does the `storage` event do?
- [ ] Why must you handle `QuotaExceededError`?
- [ ] Which API would you use to detect an element entering the viewport?
