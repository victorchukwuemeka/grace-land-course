# Module 02: HTML5 Foundations & Document Architecture

HTML (HyperText Markup Language) is the skeleton of every website. It provides structure and meaning to content before any styling (CSS) or behavior (JavaScript) is applied.

---

## 1. Anatomy of an HTML Element

An HTML element consists of an opening tag, attributes, content, and a closing tag:

```
  <p class="intro" id="greeting">Hello World</p>
  │  └───────────┬─────────────┘ └────┬────┘  │
  │              │                    │       │
Opening Tag   Attributes           Content  Closing Tag
```

### Void (Self-Closing) Elements:
Some elements cannot contain children or text content. They do not require a closing tag:
```html
<img src="photo.jpg" alt="A sunny landscape">
<input type="text" name="username">
<br>
<hr>
<meta charset="UTF-8">
<link rel="stylesheet" href="style.css">
```

---

## 2. Anatomy of a Production-Ready HTML5 Document

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Character encoding: UTF-8 supports all universal character sets -->
  <meta charset="UTF-8">

  <!-- Responsive viewport meta tag: CRITICAL for mobile layouts -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- SEO and metadata -->
  <title>Grace Frontend Engineering Academy</title>
  <meta name="description" content="Master modern frontend development from scratch.">
  <link rel="canonical" href="https://example.com/course">

  <!-- Favicon -->
  <link rel="icon" type="image/svg+xml" href="/favicon.svg">

  <!-- Stylesheets -->
  <link rel="stylesheet" href="styles.css">

  <!-- Deferred JavaScript (Executed after DOM is fully parsed) -->
  <script defer src="app.js"></script>
</head>
<body>
  <!-- All visible content goes here -->
</body>
</html>
```

### Why `<meta name="viewport" content="width=device-width, initial-scale=1.0">` is Mandatory:
Without this meta tag, mobile browsers pretend to be a 980px desktop monitor and scale the page down, causing tiny unreadable text and ruining responsive CSS media queries.

---

## 3. Block-Level vs Inline Elements

In traditional HTML/CSS layout (normal flow):

| Category | Display Behavior | Can Contain | Examples |
| :--- | :--- | :--- | :--- |
| **Block-Level** | Starts on a new line; stretches to fill 100% of parent width. Respects vertical margin/padding. | Block and inline elements | `<div>`, `<p>`, `<h1>`-`<h6>`, `<ul>`, `<ol>`, `<li>`, `<section>`, `<article>`, `<header>` |
| **Inline** | Flows within text; only takes up as much width as its content. Does NOT respect top/bottom `margin` or `height`. | Only inline elements and text | `<span>`, `<a>`, `<strong>`, `<em>`, `<code>`, `<small>` |
| **Inline-Block** | Flows like inline text, but respects `width`, `height`, and vertical `padding`/`margin`. | Other elements | `<img>`, `<button>`, `<input>`, `<select>` |

---

## 4. Text Content & Semantic Typography

```html
<!-- Heading hierarchy: strictly 1 h1 per page; never skip heading levels -->
<h1>Frontend Development Masterclass</h1>
<h2>Module 02: HTML Foundations</h2>
<h3>Text Formatting</h3>

<!-- Paragraph -->
<p>
  Learn to build fast, accessible web applications using
  <strong>strong importance</strong> (bolded by default) and
  <em>emphasized text</em> (italicized by default).
</p>

<!-- Quotes -->
<blockquote cite="https://w3.org">
  <p>The power of the Web is in its universality. Access by everyone regardless of disability is an essential aspect.</p>
  <footer>— <cite>Tim Berners-Lee</cite></footer>
</blockquote>

<!-- Inline quote -->
<p>He said, <q>Consistency is key in software engineering.</q></p>

<!-- Code snippets and preformatted text -->
<p>To initialize a Git repository, use the <code>git init</code> command.</p>

<pre><code class="language-bash">
# Terminal snippet
git status
git add .
</code></pre>
```

---

## 5. Lists: Unordered, Ordered, and Description

```html
<!-- Unordered list: for items where order does not matter -->
<ul>
  <li>HTML5</li>
  <li>CSS3</li>
  <li>JavaScript</li>
</ul>

<!-- Ordered list: for sequential steps -->
<ol>
  <li>Clone repository</li>
  <li>Install dependencies</li>
  <li>Run development server</li>
</ol>

<!-- Description List: for key-value terms or metadata -->
<dl>
  <dt>DOM</dt>
  <dd>Document Object Model: tree representation of HTML elements in memory.</dd>

  <dt>CSSOM</dt>
  <dd>CSS Object Model: tree representation of style rules.</dd>
</dl>
```
