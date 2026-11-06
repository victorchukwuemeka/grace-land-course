# Fetch API, HTTP & Working with APIs

Almost every modern frontend talks to a server. This file covers the HTTP fundamentals you need, then the `fetch` API, error handling, cancellation, and practical API patterns.

---

## 1. HTTP in 60 Seconds

An HTTP request has a **method**, **URL**, **headers**, and optional **body**. A response has a **status code**, headers, and body.

### Methods

| Method | Purpose | Idempotent? |
| :--- | :--- | :--- |
| `GET` | Read a resource | Yes |
| `POST` | Create a resource | No |
| `PUT` | Replace a resource | Yes |
| `PATCH` | Partially update | No |
| `DELETE` | Remove a resource | Yes |

### Status codes

| Range | Meaning | Examples |
| :--- | :--- | :--- |
| `2xx` | Success | `200 OK`, `201 Created`, `204 No Content` |
| `3xx` | Redirect | `301 Moved Permanently`, `304 Not Modified` |
| `4xx` | Client error | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `429 Too Many Requests` |
| `5xx` | Server error | `500 Internal Server Error`, `503 Service Unavailable` |

> **Key distinction:** `401` means "not authenticated"; `403` means "authenticated but not allowed."

### Headers

```http
Content-Type: application/json
Accept: application/json
Authorization: Bearer <token>
Cache-Control: no-cache
```

---

## 2. Basic `fetch`

```js
const response = await fetch("https://api.example.com/users");

if (!response.ok) {
  throw new Error(`HTTP ${response.status}: ${response.statusText}`);
}

const users = await response.json();
```

> **Critical:** `fetch` only rejects on **network failure**. A `404` or `500` still resolves. You must check `response.ok` manually.

### POST with JSON

```js
const response = await fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Accept: "application/json",
  },
  body: JSON.stringify({
    name: "Ada Lovelace",
    email: "ada@example.com",
  }),
});

if (response.status === 201) {
  const created = await response.json();
}
```

### Reading the body

The body can be consumed **once**:

```js
await response.json();   // parse JSON
await response.text();   // plain text / HTML
await response.blob();   // binary (images, files)
await response.arrayBuffer();
await response.formData();
```

> **Gotcha:** Calling `response.json()` twice throws `TypeError: body stream already read`. Clone first if you need multiple reads: `response.clone()`.

---

## 3. A Production-Ready Request Helper

```js
async function request(url, options = {}) {
  const headers = new Headers(options.headers);
  headers.set("Accept", "application/json");

  if (options.body && !(options.body instanceof FormData)) {
    headers.set("Content-Type", "application/json");
  }

  const response = await fetch(url, { ...options, headers });

  if (!response.ok) {
    let detail = response.statusText;
    try {
      const data = await response.json();
      detail = data.message ?? detail;
    } catch {
      // response was not JSON
    }
    throw new Error(`Request failed (${response.status}): ${detail}`);
  }

  if (response.status === 204) return null;
  return response.json();
}
```

```js
const users = await request("/api/users");
const created = await request("/api/users", {
  method: "POST",
  body: JSON.stringify({ name: "Grace" }),
});
```

---

## 4. Cancellation with `AbortController`

```js
const controller = new AbortController();

const promise = fetch("/api/search?q=css", {
  signal: controller.signal,
});

// Cancel
controller.abort();

try {
  await promise;
} catch (error) {
  if (error.name === "AbortError") {
    console.log("Request cancelled");
  } else {
    throw error;
  }
}
```

### Timeout

```js
async function fetchWithTimeout(url, ms = 8000) {
  const controller = new AbortController();
  const timer = setTimeout(() => controller.abort(), ms);

  try {
    return await fetch(url, { signal: controller.signal });
  } finally {
    clearTimeout(timer);
  }
}
```

### Cancel on new search input

```js
let currentController;

async function search(query) {
  currentController?.abort(); // cancel the previous request
  currentController = new AbortController();

  try {
    const res = await fetch(`/api/search?q=${encodeURIComponent(query)}`, {
      signal: currentController.signal,
    });
    render(await res.json());
  } catch (error) {
    if (error.name !== "AbortError") showError(error);
  }
}
```

---

## 5. REST API Design

A typical REST resource:

```http
GET    /api/posts          # list
GET    /api/posts/42       # detail
POST   /api/posts          # create
PUT    /api/posts/42       # replace
PATCH  /api/posts/42       # partial update
DELETE /api/posts/42       # delete
```

### Query parameters

```js
const params = new URLSearchParams({
  page: "2",
  limit: "20",
  sort: "-createdAt",
  search: "css grid",
});

const res = await fetch(`/api/posts?${params}`);
```

> Always use `URLSearchParams` or `encodeURIComponent` for user input. Never concatenate raw strings into a URL.

### Pagination

```js
async function* paginate(url) {
  let next = url;

  while (next) {
    const res = await fetch(next);
    const data = await res.json();
    yield data.items;
    next = data.next;
  }
}

for await (const page of paginate("/api/posts?page=1")) {
  render(page);
}
```

The `Link` header is another common pagination mechanism:

```js
const link = response.headers.get("Link"); // <...>; rel="next"
```

---

## 6. Error Handling Strategy

```js
async function loadPosts() {
  setState({ status: "loading" });

  try {
    const res = await fetch("/api/posts");

    if (!res.ok) {
      throw new HttpError(res.status, res.statusText);
    }

    const posts = await res.json();
    setState({ status: "success", data: posts });
  } catch (error) {
    if (error.name === "AbortError") return;

    if (!navigator.onLine) {
      setState({ status: "offline" });
    } else if (error instanceof HttpError && error.status === 401) {
      redirectToLogin();
    } else {
      setState({ status: "error", message: "Could not load posts." });
    }
  }
}
```

Always design UI states:

- **Loading** — skeleton or spinner.
- **Success** — data.
- **Empty** — friendly "nothing here yet."
- **Error** — actionable message + retry.
- **Offline** — cached or queued content.

> **Never** show a raw exception message to users. Log it, show a human-readable message.

---

## 7. CORS

Browsers enforce the **same-origin policy**. Cross-origin requests need CORS headers from the server:

```http
Access-Control-Allow-Origin: https://your-app.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```

- A **preflight** `OPTIONS` request is sent for non-simple requests.
- `credentials: "include"` requires `Access-Control-Allow-Credentials: true` and a specific origin (not `*`).
- CORS is a **browser** restriction. `curl` and server-to-server calls are not blocked.

> **Debugging:** The browser console usually states exactly which header is missing. Fix it on the server, not with a proxy hack in production.

---

## 8. Authentication Patterns

### Bearer token

```js
const res = await fetch("/api/me", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

### Cookies

```js
const res = await fetch("/api/me", {
  credentials: "include", // send HttpOnly cookies
});
```

> **Security:** Prefer `HttpOnly`, `Secure`, `SameSite` cookies for session tokens. If you store tokens in `localStorage`, any XSS can read them.

### Refresh flow

```js
async function apiFetch(url, options = {}) {
  let res = await fetch(url, options);

  if (res.status === 401) {
    const refreshed = await refreshToken();
    if (!refreshed) throw new Error("Session expired");

    res = await fetch(url, {
      ...options,
      headers: { ...options.headers, Authorization: `Bearer ${accessToken}` },
    });
  }

  return res;
}
```

---

## 9. Caching and Revalidation

```js
// Force network, fall back to cache
fetch(url, { cache: "no-cache" });

// Use cache first, revalidate in background
fetch(url, { cache: "stale-while-revalidate" });

// Only cache
fetch(url, { cache: "only-if-cached", mode: "same-origin" });
```

HTTP caching headers:

```http
Cache-Control: public, max-age=3600, stale-while-revalidate=86400
ETag: "abc123"
```

> **For real apps:** Use a data-fetching library (TanStack Query, SWR) for caching, deduplication, retries, and background revalidation. Build the mental model first, then use the tool.

---

## 10. Working with Third-Party APIs

```js
const API_KEY = import.meta.env.VITE_WEATHER_KEY;

async function getWeather(city) {
  const url = new URL("https://api.openweathermap.org/data/2.5/weather");
  url.searchParams.set("q", city);
  url.searchParams.set("units", "metric");
  url.searchParams.set("appid", API_KEY);

  const res = await fetch(url);
  if (!res.ok) throw new Error(`Weather API error: ${res.status}`);
  return res.json();
}
```

> **Never commit API keys.** Frontend keys are public by definition; restrict them by domain and scope. Truly secret keys belong on a server.

---

## 11. Fetch Checklist

- [ ] `response.ok` is checked.
- [ ] Network errors and HTTP errors are handled separately.
- [ ] Requests are cancellable.
- [ ] Loading, empty, and error states exist.
- [ ] Inputs are URL-encoded.
- [ ] JSON bodies set `Content-Type`.
- [ ] Secrets are not in source control.
- [ ] No unhandled promise rejections.

---

## ✅ Checkpoint

- [ ] Does `fetch` reject on a `404`? Why or why not?
- [ ] What does `AbortController` do?
- [ ] What is CORS and where is it enforced?
- [ ] Why can `response.json()` only be called once?
- [ ] How do you send cookies with `fetch`?
- [ ] Why should you never put a secret API key in frontend code?
