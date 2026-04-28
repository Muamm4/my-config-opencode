# Modern CSS Features

Advanced CSS features for modern web development.

## Container Queries

Query styles based on a container's size, not viewport.

### Setup

```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

/* Query based on container width */
@container card (min-width: 400px) {
  .card {
    display: flex;
    flex-direction: row;
  }
}
```

### Types

- `container-type: inline-size` - Query width only
- `container-type: size` - Query width and height
- `container-type: normal` - No queries (default)

### Use Cases

- **Card Components**: Different layouts based on available space
- **Sidebar**: Collapse to drawer when narrow
- **Media**: Switch between stack/grid based on space

## :has() Selector

Parent and peer selector - apply styles based on children or siblings.

### Parent Selection

```css
/* Card with image gets different styling */
.card:has(img) {
  grid-template-rows: 200px auto;
}

/* Form with error state */
.form:has(.error-message) .submit-btn {
  background-color: red;
}
```

### Child Detection

```css
/* Checkbox checked state */
.card:has(input[type="checkbox"]:checked) {
  border-color: green;
}
```

### Sibling Selection (Using + or ~)

```css
/* Label turns blue when input is focused */
input:focus + label,
input:focus + label + .hint {
  color: blue;
}

/* All items after checked radio */
input[type="radio"]:checked ~ .description {
  display: block;
}
```

### Complex Patterns

```css
/* Not has - apply when NOT containing X */
.card:not(:has(img)) {
  background: linear-gradient(...);
}

/* Multiple conditions */
.card:has(.badge):has(.featured) {
  border: 2px solid gold;
}
```

## CSS Variables (Custom Properties)

Scoped, themeable values.

### Definition

```css
:root {
  --color-primary: #3b82f6;
  --color-surface: #ffffff;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --spacing-lg: 2rem;
  --font-family: system-ui, sans-serif;
}
```

### Usage

```css
.button {
  background-color: var(--color-primary);
  padding: var(--spacing-sm) var(--spacing-md);
  font-family: var(--font-family);
}
```

### Fallbacks

```css
/* Provide fallback if variable undefined */
.container {
  background-color: var(--color-surface, #ffffff);
}
```

### Runtime Changes

```javascript
// CSS variables can be changed at runtime
document.documentElement.style.setProperty('--color-primary', '#newvalue');
```

### Scoped Variables

```css
.component {
  --component-bg: #f0f0f0;
}

.component-specific {
  background-color: var(--component-bg);
}
```

## Logical Properties

Flow-relative properties that adapt to writing direction.

### Properties Mapping

| Physical | Logical |
|---------|---------|
| `margin-left` | `margin-inline-start` |
| `margin-right` | `margin-inline-end` |
| `padding-left` | `padding-inline-start` |
| `padding-right` | `padding-inline-end` |
| `width` | `inline-size` |
| `height` | `block-size` |
| `top` | `inset-block-start` |
| `bottom` | `inset-block-end` |

### Usage

```css
/* Adapts to RTL automatically */
.card {
  padding-inline: 1.5rem;
  margin-block: 1rem;
  border-inline-start: 3px solid var(--primary);
}
```

### Text Alignment

```css
.text {
  text-align: start; /* vs 'left' or 'right' */
}

/* Float - use in flow direction */
.icon {
  float: inline-start;
}
```

## Modern Selectors

### :is() / :where()

```css
/* :is - pseudo element counts as element */
:is(h1, h2, h3):is(.title, .heading) {
  font-weight: bold;
}

/* :where - no specificity */
:where(.warning) {
  /* specificity: 0 */
}
```

### :not()

```css
/* Complex :not */
:not(:is(input, textarea, select)) {
  /* All except form controls */
}
```

### :placeholder-shown

```css
input:not(:placeholder-shown) + .error {
  display: none;
}
```

## Browser Support

- **:has()**: Chrome 105+, Firefox 121+, Safari 15.4+
- **Container Queries**: Chrome 105+, Firefox 110+, Safari 16+
- **Logical Properties**: Widely supported
- **:is()/:where()**: Widely supported