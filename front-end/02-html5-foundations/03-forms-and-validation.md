# Module 02: Forms, Controls & Native Client-Side Validation

Forms are the primary interactive bridge between users and web applications. Poorly built forms cause user frustration, accessibility barriers, and lost conversions.

---

## 1. The `<form>` Element Fundamentals

```html
<form action="/api/v1/register" method="POST" enctype="multipart/form-data" autocomplete="on">
  <!-- Controls go here -->
</form>
```

- **`action`**: The URL where form data is submitted.
- **`method`**:
  - `GET`: Appends form data to the URL as query parameters (`?query=frontend&page=1`). Use for searches or filters (idempotent operations). **Never use GET for sensitive data like passwords.**
  - `POST`: Sends form data in the HTTP request body. Use for creating or mutating data (logins, orders, registrations).
- **`enctype`**:
  - `application/x-www-form-urlencoded`: Default encoding for standard text inputs.
  - `multipart/form-data`: **Mandatory** whenever users upload files (`<input type="file">`).

---

## 2. Accessible Labeling: The Golden Rule

Every form input **must** be tied to a `<label>`.

### Explicit Association (Best Practice):
```html
<label for="user-email">Email Address <span aria-hidden="true">*</span></label>
<input type="email" id="user-email" name="email" required autocomplete="email">
```
- The `for` attribute on `<label>` matches the `id` attribute on `<input>`.
- Clicking the label focuses the input (crucial on mobile touch targets).
- Screen readers announce the label when the input receives focus.

---

## 3. Form Input Types & Built-In Validation

HTML5 provides rich input types that trigger mobile keyboard optimizations (e.g. numeric keypad, `@` symbol) and native browser validation:

```html
<form id="signup-form" novalidate>
  <!-- Text with length validation -->
  <div class="form-group">
    <label for="username">Username</label>
    <input
      type="text"
      id="username"
      name="username"
      required
      minlength="3"
      maxlength="20"
      pattern="^[a-zA-Z0-9_]+$"
      title="Username must be 3-20 characters and contain only letters, numbers, and underscores"
      placeholder="e.g. alex_dev"
    >
  </div>

  <!-- Email with built-in format check -->
  <div class="form-group">
    <label for="email">Email</label>
    <input type="email" id="email" name="email" required placeholder="you@domain.com">
  </div>

  <!-- Password with security pattern -->
  <div class="form-group">
    <label for="password">Password</label>
    <input
      type="password"
      id="password"
      name="password"
      required
      minlength="8"
      autocomplete="new-password"
    >
  </div>

  <!-- Number with min, max, step -->
  <div class="form-group">
    <label for="age">Age</label>
    <input type="number" id="age" name="age" min="18" max="120" step="1" required>
  </div>

  <!-- Telephone with regex pattern -->
  <div class="form-group">
    <label for="phone">Phone Number</label>
    <input
      type="tel"
      id="phone"
      name="phone"
      pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}"
      placeholder="123-456-7890"
    >
  </div>

  <!-- File Upload -->
  <div class="form-group">
    <label for="avatar">Profile Avatar</label>
    <input type="file" id="avatar" name="avatar" accept="image/png, image/jpeg, image/webp">
  </div>

  <!-- Date picker -->
  <div class="form-group">
    <label for="start-date">Start Date</label>
    <input type="date" id="start-date" name="startDate">
  </div>
</form>
```

---

## 4. Grouping Controls with `<fieldset>` and `<legend>`

When multiple inputs belong to the same logical question (such as radio buttons or addresses), group them using `<fieldset>` and `<legend>`:

```html
<fieldset>
  <legend>Select Your Experience Level</legend>

  <div class="radio-option">
    <input type="radio" id="exp-beginner" name="experience" value="beginner" checked>
    <label for="exp-beginner">Beginner (0 - 1 years)</label>
  </div>

  <div class="radio-option">
    <input type="radio" id="exp-intermediate" name="experience" value="intermediate">
    <label for="exp-intermediate">Intermediate (2 - 4 years)</label>
  </div>

  <div class="radio-option">
    <input type="radio" id="exp-advanced" name="experience" value="advanced">
    <label for="exp-advanced">Senior / Lead (5+ years)</label>
  </div>
</fieldset>
```

---

## 5. Modern Selects and Datalists

### The `<datalist>`: Searchable Autocomplete
Provides a native search suggestion dropdown while still allowing custom text:

```html
<label for="tech-stack">Primary Frontend Framework</label>
<input list="framework-suggestions" id="tech-stack" name="techStack" placeholder="Type or select...">

<datalist id="framework-suggestions">
  <option value="Vanilla JS">
  <option value="React">
  <option value="Vue">
  <option value="Angular">
  <option value="Svelte">
  <option value="SolidJS">
</datalist>
```

---

## 6. CSS Styling Hooks for Forms

Modern CSS supports powerful state pseudo-classes:
```css
/* Style valid and invalid inputs */
input:required {
  border-left: 3px solid #3b82f6;
}

/* :user-invalid only highlights errors AFTER user has touched and left the input */
input:user-invalid {
  border-color: #ef4444;
  background-color: #fef2f2;
}

/* Clear visual focus outline for accessibility */
input:focus-visible,
button:focus-visible {
  outline: 2px solid #2563eb;
  outline-offset: 2px;
}
```
