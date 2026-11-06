# Module 05: Hands-On Projects

Reading about the web is not the same as building it. These three projects apply everything from Modules 01–04. Each includes requirements, milestones, and a review rubric.

> **Rules:** Vanilla HTML, CSS, and JavaScript only. No frameworks, no UI libraries. You may use a bundler and small utilities if you understand what they do.

---

## Project 1: Developer Portfolio

**Goal:** A fast, accessible personal site that presents who you are and what you have built.

### Requirements

- [ ] Semantic HTML: `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`.
- [ ] Responsive from 320px to ultrawide.
- [ ] Light/dark theme with `localStorage` persistence.
- [ ] Project cards with images, descriptions, tech tags, and links.
- [ ] Contact form with client-side validation.
- [ ] Keyboard accessible navigation and visible focus styles.
- [ ] Lighthouse: Performance ≥ 90, Accessibility ≥ 95, Best Practices ≥ 95, SEO ≥ 90.
- [ ] Deployed to GitHub Pages, Netlify, or Vercel.

### Stretch

- Scroll-triggered reveal animations with `IntersectionObserver`.
- A blog section generated from Markdown or JSON.
- Open Graph images and structured data (`JSON-LD`).
- `prefers-reduced-motion` support.

### Milestones

1. Content inventory and wireframe.
2. Semantic HTML shell.
3. Design tokens and layout.
4. Responsive polish.
5. Interactions and form validation.
6. Accessibility and performance audit.
7. Deploy.

---

## Project 2: Task & Kanban Board

**Goal:** A local-first task manager with drag-and-drop and persistent state.

### Requirements

- [ ] Columns: **Backlog**, **In Progress**, **Done**.
- [ ] Create, edit, delete, and move tasks.
- [ ] Drag and drop between columns (HTML Drag and Drop API or Pointer Events).
- [ ] Keyboard alternative for moving tasks (buttons + menu).
- [ ] Persist tasks in `localStorage`.
- [ ] Filter by priority and search by text.
- [ ] Empty, loading (simulated), and error states.
- [ ] Accessible announcements when a task moves (`aria-live`).
- [ ] No `innerHTML` with user input.

### Data model

```js
const task = {
  id: crypto.randomUUID(),
  title: "Finish CSS Grid lesson",
  description: "Complete the gallery challenge",
  status: "todo",          // todo | doing | done
  priority: "high",        // low | medium | high
  createdAt: Date.now(),
  updatedAt: Date.now(),
};
```

### Stretch

- Undo/redo with a command history.
- Subtasks and due dates.
- Export/import JSON.
- Sync across tabs with `BroadcastChannel`.

### Milestones

1. Static three-column layout with Grid.
2. Render tasks from an array.
3. Add/edit/delete with event delegation.
4. Persist and hydrate from `localStorage`.
5. Drag and drop.
6. Keyboard and screen-reader support.
7. Filters, search, and empty states.

---

## Project 3: Real-Time Weather Dashboard

**Goal:** Fetch and display live weather data with robust loading, error, and offline handling.

### Requirements

- [ ] Search by city with debounced input.
- [ ] Current conditions: temperature, description, humidity, wind, icon.
- [ ] 5-day forecast.
- [ ] Loading skeleton, error message with retry, and empty state.
- [ ] Cancel in-flight requests with `AbortController`.
- [ ] Cache the last successful result in `localStorage`.
- [ ] Toggle between °C and °F; persist the preference.
- [ ] Handle geolocation permission denial gracefully.
- [ ] Responsive layout with Grid.
- [ ] API key stored in an environment variable, not source control.

### Suggested API

- [OpenWeatherMap](https://openweathermap.org/api) (free tier)
- [Open-Meteo](https://open-meteo.com/) (no key required)

### Stretch

- Hourly chart with the Canvas API or inline SVG.
- Background gradient that reflects the current condition.
- Auto-refresh every 10 minutes with a visible "last updated" time.
- Offline fallback using the Cache API.

### Milestones

1. Static layout with sample data.
2. Fetch current weather and render.
3. Loading/error/empty states.
4. Forecast and unit toggle.
5. Debounce + abort.
6. Caching and offline behavior.
7. Accessibility and performance pass.

---

## Review Rubric

| Area | 1 — Needs work | 3 — Solid | 5 — Professional |
| :--- | :--- | :--- | :--- |
| **HTML** | Divs everywhere | Semantic sections | Accessible landmarks, correct headings |
| **CSS** | Works at one size | Responsive, consistent tokens | Fluid, themed, no dead CSS |
| **JS** | Procedural, global state | Modules and clear functions | Tested, resilient, no leaks |
| **Accessibility** | Mouse-only | Labels and focus styles | Keyboard + screen-reader complete |
| **Performance** | Unoptimized images | Lazy loading, minified assets | Measured Core Web Vitals |
| **UX** | Happy path only | Loading and error states | Thoughtful empty/offline states |
| **Code quality** | Hard to follow | Consistent naming | Documented trade-offs, small modules |

---

## Submission Checklist

- [ ] Public repository with a clear README.
- [ ] Screenshots or a live demo link.
- [ ] Setup instructions.
- [ ] No secrets, `node_modules`, or build output committed.
- [ ] Responsive at 320px, 768px, 1440px.
- [ ] Keyboard navigation tested.
- [ ] Lighthouse report attached.
- [ ] A short "what I learned" section.

> **Portfolio advice:** Three finished, polished projects beat ten unfinished ones. Write about your decisions, not just your features.
