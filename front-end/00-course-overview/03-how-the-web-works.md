# Module 00: How the Web Works & The Critical Rendering Path

To write high-performance web applications, you must understand what happens under the hood when a user types a URL into a browser address bar and hits `Enter`.

---

## 1. The High-Level Network Journey

```
┌──────────┐ 1. DNS Query ┌─────────────┐
│  Client  ├─────────────►│ DNS Server  │
│ (Browser)│◄─────────────┤ (Find IP)   │
└────┬─────┘ 2. IP Address└─────────────┘
     │
     │ 3. TCP Handshake + TLS (HTTPS)
     ├───────────────────────────────────►┌──────────────┐
     │ 4. HTTP GET /index.html            │  Web Server  │
     │◄───────────────────────────────────┤ (Nginx/Node) │
     │ 5. HTTP Response (200 OK + HTML)   └──────────────┘
```

### Step 1: DNS Resolution (Domain Name System)
1. Browser checks its local DNS cache.
2. OS cache is checked.
3. Router cache & ISP Resolver are queried.
4. If not found, recursive DNS queries resolve the domain (e.g. `example.com`) to an IP address (e.g., `93.184.216.34`).

### Step 2: TCP Handshake & TLS Negotiation
- **TCP 3-Way Handshake**:
  - `SYN` (Synchronize): Client sends request to connect.
  - `SYN-ACK` (Synchronize-Acknowledge): Server acknowledges.
  - `ACK` (Acknowledge): Client confirms connection.
- **TLS Handshake** (for `https://`): Establishes symmetric encryption keys to protect data in transit.

### Step 3: The HTTP Request/Response Lifecycle
The browser sends an HTTP GET request:
```http
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 ...
Accept: text/html,application/xhtml+xml...
```

The web server responds with an HTTP status code, headers, and the payload:
```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Content-Length: 4231
Cache-Control: max-age=3600

<!DOCTYPE html>
<html>
...
```

#### Common HTTP Status Codes Every Frontend Dev Must Know:
- **`200 OK`**: Request succeeded.
- **`201 Created`**: Resource created (common after POST).
- **`301 Moved Permanently` / `302 Found`**: Redirection.
- **`304 Not Modified`**: Browser can use cached copy.
- **`400 Bad Request`**: Client sent invalid syntax/data.
- **`401 Unauthorized`**: Authentication is required.
- **`403 Forbidden`**: Authenticated, but not permitted.
- **`404 Not Found`**: Resource does not exist.
- **`500 Internal Server Error`**: Server crashed or failed.
- **`502 Bad Gateway` / `503 Service Unavailable`**: Server unreachable or overloaded.

---

## 2. The Browser's Critical Rendering Path (CRP)

Once the browser receives the raw HTML bytes, it must convert them into pixels on the screen. This sequence is known as the **Critical Rendering Path**.

```
  Raw HTML Bytes
       │
       ▼
   Tokenizing & Parsing ─────────► [ DOM Tree ]
                                         │
  Raw CSS Bytes                          │
       │                                 ▼
       ▼                          [ Render Tree ]
   Tokenizing & Parsing ─────────► (Visible Nodes + Computed Styles)
       │                                 │
       ▼                                 ▼
   [ CSSOM Tree ]                   [ Layout (Reflow) ]
                                    (Computes Geometry: x, y, width, height)
                                         │
                                         ▼
                                    [ Paint (Repaint) ]
                                    (Fills pixels: colors, borders, shadows)
                                         │
                                         ▼
                                  [ Compositing ]
                                  (GPU combines layers to screen)
```

### 1. Constructing the DOM (Document Object Model)
- Bytes `48 65 6c 6c 6f` $\rightarrow$ Characters `<html>` $\rightarrow$ Tokens $\rightarrow$ Nodes $\rightarrow$ **DOM Tree**.
- Represents the structural hierarchy of HTML elements.

### 2. Constructing the CSSOM (CSS Object Model)
- CSS is **render-blocking**: The browser will not render any page content until it has completely downloaded and parsed all linked stylesheets.
- This prevents the "Flash of Unstyled Content" (FOUC).

### 3. The Render Tree
- Combines DOM and CSSOM.
- **Important**: Elements with `display: none;` are **NOT** in the Render Tree (they take up zero space and are not painted).
- Elements with `visibility: hidden;` **ARE** in the Render Tree (they take up space, even though invisible).

### 4. Layout (Reflow)
- Computes exact coordinates and bounding box sizes for every visible node relative to the viewport.

### 5. Paint (Rasterization)
- Converts vector boxes into actual colored pixels in browser memory buffers.

### 6. Compositing
- Assembles multiple layers (e.g., elements with CSS transforms or `will-change`) on the GPU to draw the final screen image.

---

## 3. How JavaScript Affects the Rendering Path

By default, `<script>` tags are **parser-blocking**:
```html
<!-- 🛑 BLOCKING: HTML parser stops, browser downloads script, executes it, then resumes HTML parsing -->
<script src="app.js"></script>
```

### Modern Script Loading Attributes:
```
HTML Parsing:    ======== [paused] =====================>
Script Download:          ========
Script Execute:                   ====

VS `defer`:
HTML Parsing:    =======================================>
Script Download:       ========
Script Execute:                                         ==== (Runs after DOM is ready, in order)

VS `async`:
HTML Parsing:    ============= [paused] ================>
Script Download:       ========
Script Execute:                ==== (Runs as soon as downloaded, order NOT guaranteed)
```

### Best Practice:
- Use `<script defer src="app.js"></script>` in the `<head>` for scripts that need the full DOM tree and preserve execution order.
- Use `async` only for independent third-party trackers or analytics that do not depend on the DOM or other scripts.
