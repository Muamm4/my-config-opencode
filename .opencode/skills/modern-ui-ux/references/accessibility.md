# Accessibility (a11y)

Ensuring digital interfaces are usable by everyone, regardless of ability.

## Core Requirements

### 1. Semantic HTML
- Use proper HTML elements (`<button>`, `<a>`, `<input>`, `<header>`, `<main>`, `<nav>`)
- Don't use `<div>` for interactive elements
- Ensure logical heading hierarchy (h1 → h2 → h3, never skip levels)
- landmarks: `<header>`, `<nav>`, `<main>`, `<footer>`, `<aside>`, `<form>`

### 2. ARIA Labels
- **When to use**: When visual text isn't sufficient or isn't present
- `aria-label`: Short description for interactive elements
- `aria-describedby`: Reference to descriptive text
- `aria-live`: Announce dynamic content changes
- `aria-expanded`: State for collapsible elements
- `aria-hidden`: Hide decorative elements from screen readers

### 3. Keyboard Navigation
- **Focus order**: Logical tab order matching visual order
- **Focus visible**: Always show visible focus indicator
- **Skip links**: Provide "skip to main content" for keyboard users
- **Shortcuts**: Ensure keyboard shortcuts don't conflict with browser defaults
- **Escape**: Modal/dropdown should close on Escape key

### 4. Focus Management
- Initial focus on first interactive element when opening modals
- Return focus to trigger element when closing modals
- Prevent focus trapped in modals (except when intentional)
- Roving tabindex for radio groups

### 5. Color & Contrast
- **Minimum contrast**: 4.5:1 for normal text, 3:1 for large text
- **Don't rely on color alone**: Use icons or text to supplement color
- **Error indicators**: Combine color with text or icons

### 6. Screen Reader Compatibility
- **Alt text**: Descriptive alt for meaningful images, empty for decorative
- **Form labels**: Every input must have a visible label
- **Error messages**: Associated with form fields via `aria-describedby`
- **Dynamic content**: Announce with `aria-live` regions

## Common Patterns

### Accessible Button
```html
<button type="button" aria-label="Close menu">
  <svg aria-hidden="true">...</svg>
</button>
```

### Accessible Form Input
```html
<label for="email">Email address</label>
<input 
  type="email" 
  id="email" 
  name="email"
  aria-describedby="email-hint"
  autocomplete="email"
/>
<p id="email-hint" class="hint">We'll never share your email</p>
```

### Accessible Modal
```html
<div 
  role="dialog" 
  aria-modal="true" 
  aria-labelledby="modal-title"
>
  <h2 id="modal-title">Confirm deletion</h2>
  <!-- content -->
</div>
```

### Focus Trap (for modals)
```javascript
function trapFocus(container) {
  const focusable = container.querySelectorAll(
    'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  const first = focusable[0];
  const last = focusable[focusable.length - 1];
  
  container.addEventListener('keydown', (e) => {
    if (e.key !== 'Tab') return;
    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault();
      last.focus();
    } else if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault();
      first.focus();
    }
  });
}
```

## Testing Tools

- **Automated**: axe-core, Lighthouse, WAVE
- **Manual**: Keyboard-only navigation, screen reader testing (NVDA, VoiceOver)
- **Extensions**: Accessibility Insights, axe DevTools

## Best Practices

1. **Build in accessibility**: Don't retrofit; design and build with a11y in mind
2. **Test with real users**: Include people with disabilities in testing
3. **Use semantic HTML**: The foundation of accessibility
4. **Respect user preferences**: Honor `prefers-reduced-motion`, `prefers-color-scheme`