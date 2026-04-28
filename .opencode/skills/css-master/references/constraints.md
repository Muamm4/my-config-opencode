# CSS Best Practices & Constraints

Non-negotiable rules for maintainable, accessible CSS.

## The "Never" Rules

### Never Use !important

```css
/* BAD */
.override {
  background-color: red !important;
}

/* GOOD - Find the specificity conflict and fix properly */
.parent .specific-target {
  background-color: red;
}
```

**Exceptions**: Only for third-party styles that cannot be overridden any other way, and always comment why:

```css
/* Third-party framework override - targeting is too generic */
/* Issue: Bootstrap .btn applies to all buttons */
/* TODO: Remove when framework is removed */
.override-plugin {
  background-color: red !important;
}
```

## Avoid Fixed Dimensions

```css
/* BAD - Rigid, breaks on resize */
.fixed {
  width: 300px;
  height: 200px;
}

/* GOOD - Flexible, responsive */
.fluid {
  max-width: 300px;
  min-height: 200px;
  width: 100%;
}
```

### Relative Units

| Unit | Use Case |
|------|---------|
| `rem` | Font sizes, spacing (scales with root) |
| `em` | Compound with parent font-size |
| `%` | Layout, grids |
| `vh` / `vw` | Full-height sections |
| `vmin` / `vmax` | Responsive elements |

```css
/* Good relative usage */
html {
  font-size: 16px;
}

.card {
  padding: 1.5rem;     /* 24px - scales with html */
  font-size: 1rem;    /* 16px */
  width: 100%;        /* Percent of parent */
  max-width: 60ch;   /* Optimal reading width */
}
```

## Accessibility

### Color Contrast (WCAG 2.1)

```css
/* Minimum contrast ratios */
/* AA: 4.5:1 for normal text */
/* AA: 3:1 for large text */
/* AAA: 7:1 for normal, 4.5:1 for large */

.text-primary {
  color: #1a1a1a;  /* Contrast: 16:1 on white */
}

.text-secondary {
  color: #5c5c5c;   /* Contrast: 7:1 on white */
}
```

### Focus States

```css
/* Always visible focus */
button:focus-visible,
a:focus-visible,
input:focus-visible {
  outline: 2px solid blue;
  outline-offset: 2px;
}

/* Remove outline only if replacing */
.replaced-focus {
  outline: none;
  ring: /* alternative indicator */;
}
```

### Semantic HTML First

```css
/* Don't style <div> like headings */
/* BAD */
div.heading {
  font-size: 2rem;
  font-weight: bold;
}

/* GOOD */
h2 {
  font-size: 2rem;
  font-weight: bold;
}
```

### Motion Safety

```css
/* Respect user preferences */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

## Maintainability

### CSS Variables for Theming

```css
:root {
  --color-primary: #3b82f6;
  --color-surface: #ffffff;
  --spacing-unit: 0.5rem;
  --radius: 0.5rem;
}

.button {
  background-color: var(--color-primary);
  padding: var(--spacing-unit) calc(var(--spacing-unit) * 2);
  border-radius: var(--radius);
}
```

### Namespace Components

```css
/* BEM or similar naming */
.card { }
.card__header { }
.card__body { }
.card--featured { }

/* Or namespace */
.c-card__header { }
.js-card { }  /* JavaScript hooks only */
```

### Document Patterns

```css
/**
 * Component: Card
 * 
 * Displays content in a styled container
 * 
 * Markup:
 * <div class="card">
 *   <div class="card__header"></div>
 *   <div class="card__body"></div>
 * </div>
 */
```

## Code Organization

### One Declaration Per Line

```css
/* Good */
.selector {
  property: value;
  property: value;
}

/* Bad - harder to read */
.selector { property: value; property: value; }
```

### Logical Ordering

```css
.selector {
  /* Layout */
  display: flex;
  
  /* Positioning */
  position: relative;
  
  /* Box model */
  padding: 1rem;
  margin: 1rem;
  
  /* Styling */
  background: white;
  color: black;
  
  /* Typography */
  font-size: 1rem;
  
  /* Effects */
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}
```

### Comments

```css
/* Section comment */
/* -------------------------------------------------------------------------- */

/* Complex selector explanation */
/* Targets the third item in a grid layout (n + 1 = 3) */
.grid-item:nth-child(3n+1) { }
```

## Testing Checklist

- [ ] No `!important` or documented exceptions
- [ ] No fixed `px` for responsive dimensions
- [ ] Color contrast meets WCAG AA
- [ ] Focus states visible
- [ ] Respects `prefers-reduced-motion`
- [ ] Works at 200% zoom
- [ ] Responsive at all breakpoints