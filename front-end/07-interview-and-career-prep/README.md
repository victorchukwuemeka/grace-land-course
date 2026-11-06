# Module 07: Interview & Career Preparation

Technical interviews test how you think, communicate, and debug — not just what you memorized. This module gives you the questions, frameworks, and habits to interview well.

---

## 1. The Interview Loop

| Stage | What they assess |
| :--- | :--- |
| Recruiter screen | Motivation, timeline, salary range, basic fit |
| Technical screen | HTML/CSS/JS fundamentals, a small coding task |
| Take-home / live coding | Problem solving, code quality, testing |
| System design (frontend) | Component architecture, state, performance, accessibility |
| Behavioral | Collaboration, ownership, conflict, learning |
| Team match | Communication and working style |

---

## 2. Core HTML Questions

1. What is the difference between `<div>` and `<section>`?
2. When should you use `<article>`, `<aside>`, and `<nav>`?
3. What is the accessibility tree?
4. How do you make a custom button accessible?
5. What is the difference between `alt=""` and a missing `alt` attribute?
6. How do `<label>`, `for`, and `id` connect?
7. What are ARIA roles, states, and properties? When should you avoid ARIA?
8. What is the difference between `defer` and `async` on a script?
9. How does the browser parse HTML into the DOM?
10. What is the difference between `localStorage`, cookies, and IndexedDB?

---

## 3. Core CSS Questions

1. Explain the cascade and specificity.
2. What is the box model? How does `box-sizing` change it?
3. What is a block formatting context?
4. How does margin collapsing work?
5. Explain Flexbox vs Grid and when to use each.
6. What is the difference between `position: relative`, `absolute`, `fixed`, and `sticky`?
7. What creates a stacking context?
8. How does `z-index` interact with stacking contexts?
9. What is the difference between `em`, `rem`, `vh`, and `dvh`?
10. How do you build a responsive layout without media queries?
11. What are container queries?
12. How do CSS custom properties enable theming?
13. What is the difference between `:is()`, `:where()`, and `:has()`?
14. How do cascade layers work?
15. How do you debug a layout bug?
16. How do you ensure accessible color contrast?
17. What is `prefers-reduced-motion`?
18. How do you animate performantly?
19. What is CLS and how do you prevent it?
20. How do you organize CSS in a large codebase?

---

## 4. Core JavaScript Questions

1. Explain the event loop, microtasks, and macrotasks.
2. What is the difference between `var`, `let`, and `const`?
3. What is hoisting and the Temporal Dead Zone?
4. What is a closure? Give a real use case.
5. Explain `this` binding.
6. What is the prototype chain?
7. How does `class` differ from a constructor function?
8. What is the difference between `==` and `===`?
9. How does type coercion work?
10. What are primitive vs reference types?
11. Explain shallow vs deep copy.
12. What is event delegation and why use it?
13. What is the difference between `event.target` and `event.currentTarget`?
14. What is the difference between `null` and `undefined`?
15. What are promises? How do `all`, `allSettled`, `race`, and `any` differ?
16. How does `async`/`await` work under the hood?
17. How do you handle errors in async code?
18. What is a memory leak in frontend code? How do you find one?
19. What is debouncing vs throttling?
20. How do ES modules work? What is tree shaking?
21. What is the difference between `map`, `forEach`, `filter`, and `reduce`?
22. How would you implement `deepClone`?
23. What is `structuredClone`?
24. What is the difference between `Object.freeze` and immutability?
25. How do you prevent XSS?

---

## 5. Frontend System Design

### Framework

For a component or feature, cover:

1. **Requirements** — users, scale, devices, accessibility.
2. **Data model** — entities, relationships, state ownership.
3. **Component tree** — boundaries, props, events.
4. **State management** — local vs global, server vs client.
5. **Data fetching** — caching, pagination, optimistic updates.
6. **Performance** — bundle size, rendering, images, code splitting.
7. **Accessibility** — keyboard, screen readers, focus.
8. **Error handling** — loading, empty, error, offline.
9. **Testing** — unit, integration, E2E.
10. **Trade-offs** — what you chose and why.

### Practice prompts

- Design an infinite-scroll image feed.
- Design a multi-step form with validation and draft saving.
- Design a real-time collaborative todo list.
- Design a dashboard with filters, charts, and pagination.
- Design an accessible modal system.
- Design a design system with tokens and themes.

---

## 6. Coding Interview Patterns

### Arrays and strings

- Two pointers
- Sliding window
- Frequency map
- Prefix sums

### Objects and maps

- Grouping and counting
- Caching / memoization
- Deduplication

### Async

- Parallel vs sequential
- Concurrency limiting
- Retry with backoff
- Cancellation

### DOM

- Event delegation
- Debounce/throttle
- Virtualized list (concept)
- Accessible widget state

### Example: concurrency limit

```js
async function mapLimit(items, limit, worker) {
  const results = new Array(items.length);
  let index = 0;

  async function run() {
    while (index < items.length) {
      const current = index++;
      results[current] = await worker(items[current], current);
    }
  }

  await Promise.all(
    Array.from({ length: Math.min(limit, items.length) }, run)
  );

  return results;
}
```

---

## 7. Behavioral Interview Framework (STAR)

- **Situation** — context and constraints.
- **Task** — your responsibility.
- **Action** — what you did and why.
- **Result** — measurable outcome and what you learned.

Prepare 6–8 stories covering:

- A difficult bug you fixed.
- A disagreement with a teammate.
- A project you owned end-to-end.
- A performance improvement.
- A mistake and how you handled it.
- A time you learned something quickly.
- A time you improved accessibility or quality.
- A time you prioritized under pressure.

> **Tip:** Quantify results where possible: "cut bundle size by 38%," "reduced LCP from 4.1s to 1.8s."

---

## 8. Live Coding Strategy

1. **Clarify** — inputs, outputs, edge cases, constraints.
2. **Plan out loud** — state the approach before typing.
3. **Start simple** — make it work, then optimize.
4. **Test** — walk through examples and edge cases.
5. **Refactor** — naming, duplication, complexity.
6. **Discuss trade-offs** — time, space, readability.

> **Do not go silent.** Interviewers evaluate your reasoning as much as your code.

---

## 9. Code Review Checklist

- [ ] Names describe intent.
- [ ] Functions do one thing.
- [ ] No duplicated logic.
- [ ] Edge cases handled (empty, null, error, loading).
- [ ] No secrets or debug logs.
- [ ] Accessible markup and keyboard support.
- [ ] Responsive behavior verified.
- [ ] Tests cover behavior, not implementation.
- [ ] Performance impact considered.
- [ ] Error messages are actionable.

---

## 10. Portfolio & Resume

### Portfolio

- 3 polished projects with live links and READMEs.
- Each README: problem, approach, stack, screenshots, trade-offs, what you learned.
- A personal site with clear contact information.
- Source code that is readable and consistently formatted.

### Resume bullets

Weak:

> Built a weather app with JavaScript.

Strong:

> Built a responsive weather dashboard (vanilla JS, Open-Meteo API) with debounced search, request cancellation, and cached offline fallback; reduced repeat API calls by 60% and achieved a 98 Lighthouse accessibility score.

### GitHub profile

- Pin your best repositories.
- Add a profile README.
- Keep commit history meaningful.
- Include tests and CI where practical.

---

## 11. Job Search Strategy

1. **Target** — choose role level, company size, and domain.
2. **Network** — referrals convert far better than cold applications.
3. **Apply** — tailor the resume to each role; mirror the job description's language.
4. **Prepare** — practice 2–3 coding problems per day, out loud.
5. **Track** — spreadsheet of company, stage, contact, and follow-up date.
6. **Follow up** — a short, specific thank-you note after each interview.
7. **Iterate** — after every rejection, write down one thing to improve.

### Weekly plan

| Day | Focus |
| :--- | :--- |
| Monday | 2 coding problems + review |
| Tuesday | CSS/HTML deep dive |
| Wednesday | Build a feature in a project |
| Thursday | System design + behavioral stories |
| Friday | Mock interview + applications |
| Weekend | Portfolio polish and rest |

---

## 12. Questions to Ask the Interviewer

- What does the frontend team own end-to-end?
- How do you measure performance and accessibility?
- What does the code review process look like?
- How are design and engineering decisions made?
- What does onboarding look like in the first 30/60/90 days?
- What is the biggest technical challenge the team faces right now?
- How do you balance new features with technical debt?

> **Avoid** asking only about perks. Ask about the work, the team, and how success is measured.

---

## 13. Red Flags in a Job

- No code review or testing culture.
- "We need someone who can do everything yesterday."
- Accessibility is treated as optional.
- Constant crunch and no planning.
- No clear ownership or growth path.
- Interviewers cannot describe the product or users.

---

## 14. 30-Day Interview Plan

| Week | Goal |
| :--- | :--- |
| 1 | Master HTML/CSS fundamentals; rebuild one project. |
| 2 | JavaScript deep dive: closures, async, DOM. |
| 3 | System design + 10 coding problems + mock interview. |
| 4 | Polish portfolio, resume, behavioral stories, apply daily. |

---

## Final Advice

- **Depth beats breadth.** Know the fundamentals cold.
- **Communicate.** A clear explanation of a simple solution beats silent cleverness.
- **Ship.** Finished projects are proof of ability.
- **Be curious.** Ask thoughtful questions and follow up.
- **Keep learning.** The platform changes; the mental models endure.
