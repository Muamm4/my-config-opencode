# Layout Mastery

Expertise in advanced CSS layout systems for complex responsive interfaces.

## Flexbox

### Core Patterns

**Center Content**:
```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

**Space Between**:
```css
.nav-links {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

**Stack Layout**:
```css
.column {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}
```

### Common Issues

- **Flex item squishing**: Add `flex-shrink: 0` to prevents items from shrinking below content size.
- **Flex gap not working**: Use `gap` property directly on flex container (supported in modern browsers).
- **Vertical centering**: Use `align-items: center` on parent, not `vertical-align`.

## CSS Grid

### Core Patterns

**Simple Grid**:
```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
}
```

**Holy Grail Layout**:
```css
.layout {
  display: grid;
  grid-template-areas:
    "header header header"
    "nav main aside"
    "footer footer footer";
  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}
```

**Responsive Grid**:
```css
.responsive-grid {
  display: grid;
  grid-template-columns: 1fr;
  @media (min-width: 768px) {
    grid-template-columns: repeat(2, 1fr);
  }
  @media (min-width: 1024px) {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

### Grid Functions

- `repeat(n, value)` - Repeat pattern n times
- `auto-fit` - Fit columns, wrap if needed
- `auto-fill` - Fill row with columns
- `minmax(min, max)` - Bound column sizes
- `fr` - Fractional unit

## Subgrid

Subgrid inherits grid tracks from parent, enabling alignment across nested grids.

```css
.parent-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}

.child-grid {
  display: grid;
  grid-column: span 2;
  grid-template-columns: subgrid; /* Inherits parent columns */
}

.item {
  grid-column: 2; /* Aligned with parent's column 2 */
}
```

### Browser Support

- Chrome 117+, Firefox 71+, Safari 16+, Edge 117+
- Use feature detection: `@supports grid-template-columns: subgrid`

## Responsive Patterns

### Mobile-First Approach

```css
/* Base styles (mobile) */
.container {
  padding: 1rem;
  font-size: 1rem;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
    font-size: 1.125rem;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    padding: 3rem;
    font-size: 1.25rem;
  }
}
```

## Common Pitfalls

1. **Grid items not spanning**: Use `grid-column: span n` or `grid-column: start / end`
2. **Items overflow**: Add `min-height: 0` to flex/grid children with overflow content
3. **Gap doubling**: Remove margins when using gap; gap replaces margin-based spacing
4. **100% height not working**: Ensure parent has explicit height or `min-height`