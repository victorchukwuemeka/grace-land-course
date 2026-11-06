# Module 02: Semantic HTML Architecture & Modern Elements

"Semantic HTML" means using the HTML tag that accurately describes the *meaning* and *purpose* of the content, rather than using generic `<div>` and `<span>` tags solely for visual styling.

---

## 1. Why Semantics Matter

1. **Accessibility (a11y)**: Screen readers build an accessibility tree based on landmarks (`<nav>`, `<main>`, `<header>`). Blind or visually impaired users can jump directly between sections using keyboard shortcuts.
2. **SEO (Search Engine Optimization)**: Googlebot, Bingbot, and web scrapers heavily weight semantic headings and articles when indexing content.
3. **Maintainability & Clean Code**: A team of engineers can read `<article>` and immediately understand the component boundary, rather than untangling a nested tree of 15 `<div>` elements.

---

## 2. Page Structure & Landmark Elements

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Tech Blog</title>
</head>
<body>

  <!-- Site-wide header containing logo and primary navigation -->
  <header class="site-header">
    <a href="/" class="brand-logo">GraceDev</a>
    <nav aria-label="Main Navigation">
      <ul class="nav-links">
        <li><a href="/courses">Courses</a></li>
        <li><a href="/blog">Blog</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </nav>
  </header>

  <!-- Main content area (Strictly ONE per page) -->
  <main id="main-content">

    <!-- Standalone, self-contained article -->
    <article class="blog-post">
      <header>
        <h1>Mastering CSS Grid and Flexbox in 2026</h1>
        <p class="meta">
          Published by <span class="author">Alex Mercer</span> on
          <time datetime="2026-03-15">March 15, 2026</time>
        </p>
      </header>

      <section class="post-intro">
        <h2>Introduction</h2>
        <p>CSS Grid and Flexbox solve different layout problems...</p>
      </section>

      <section class="post-body">
        <h2>When to choose Grid over Flexbox</h2>
        <p>Grid is two-dimensional; Flexbox is one-dimensional...</p>
      </section>

      <footer>
        <p>Tags: <a href="/tags/css">CSS</a>, <a href="/tags/webdev">WebDev</a></p>
      </footer>
    </article>

    <!-- Sidebar with complementary/tangential content -->
    <aside class="sidebar" aria-label="Related content">
      <h3>Recommended Articles</h3>
      <ul>
        <li><a href="#">Understanding the JavaScript Event Loop</a></li>
        <li><a href="#">Building Accessible Web Forms</a></li>
      </ul>
    </aside>

  </main>

  <!-- Site-wide footer -->
  <footer class="site-footer">
    <p>&copy; 2026 GraceDev Academy. All rights reserved.</p>
    <nav aria-label="Legal">
      <a href="/privacy">Privacy Policy</a>
      <a href="/terms">Terms of Service</a>
    </nav>
  </footer>

</body>
</html>
```

---

## 3. The Decision Matrix: `<article>` vs `<section>` vs `<div>`

| Element | Semantic Purpose | When to Use | Example |
| :--- | :--- | :--- | :--- |
| `<article>` | Self-contained, syndicatable composition | Can be distributed independently in an RSS feed, newsletter, or external widget. | Blog post, news article, forum comment, product card. |
| `<section>` | Thematic grouping of content | A distinct chapter or sub-section of a page, almost always accompanied by a heading (`<h2>`-`<h6>`). | "Features", "Pricing", "Testimonials", "Biography". |
| `<div>` | Zero semantic meaning | Only for visual styling, grid wrappers, animation wrappers, or flex parents. | `.container`, `.card-row`, `.overlay-backdrop`. |

---

## 4. Modern HTML5 UI Elements

### 1. Accordion with `<details>` and `<summary>` (Zero JS Required!)
```html
<details class="faq-item">
  <summary>Do I need to learn React before mastering JavaScript?</summary>
  <p>
    No! In fact, learning React before mastering vanilla JavaScript, the DOM, and asynchronous
    programming often leads to confusion and poor code architecture.
  </p>
</details>
```

### 2. Native Modals with `<dialog>`
The `<dialog>` element provides built-in accessibility, backdrop dimming, ESC key handling, and focus trapping out of the box:

```html
<dialog id="subscribe-dialog" class="modal">
  <h2>Subscribe to Newsletter</h2>
  <p>Get weekly frontend engineering tips delivered to your inbox.</p>
  <form method="dialog">
    <label for="email-input">Email Address</label>
    <input type="email" id="email-input" required>
    <menu>
      <button value="cancel">Cancel</button>
      <button type="submit" value="confirm">Subscribe</button>
    </menu>
  </form>
</dialog>

<button onclick="document.getElementById('subscribe-dialog').showModal()">
  Open Newsletter Dialog
</button>
```

### 3. Media Captions with `<figure>` and `<figcaption>`
```html
<figure>
  <img src="event-loop.png" alt="Diagram showing Call Stack, Web APIs, Task Queue, and Microtask Queue">
  <figcaption>Figure 1: The JavaScript runtime concurrency and event loop model.</figcaption>
</figure>
```
