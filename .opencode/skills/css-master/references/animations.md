# CSS Animations

Transitions, keyframes, and orchestration.

## CSS Transitions

### Basic Syntax

```css
.button {
  /* property duration easing */
  transition: background-color 0.2s ease-in-out;
  
  /* Multiple properties */
  transition: transform 0.2s, background-color 0.3s ease-out;
  
  /* All properties */
  transition: all 0.3s;
}
```

### Timing Functions

```css
/* Standard */
transition: transform 0.3s ease;        /* Default - slow start/end */

/* Linear */
transition: transform 0.3s linear;     /* Constant speed */

/* Specific */
transition: transform 0.3s ease-in;      /* Slow start */
transition: transform 0.3s ease-out;     /* Slow end */
transition: transform 0.3s ease-in-out;  /* Slow start and end */

/* Custom */
transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
```

### Common Patterns

```css
/* Hover effect */
.button {
  background-color: blue;
  transition: background-color 0.2s;
}
.button:hover {
  background-color: darkblue;
}

/* Scale on hover */
.card {
  transition: transform 0.2s ease;
}
.card:hover {
  transform: scale(1.05);
}

/* Color and shadow */
.button {
  transition: background-color 0.2s, box-shadow 0.2s;
}
.button:hover {
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}
```

## Keyframe Animations

### Definition

```css
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulse {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
}
```

### Usage

```css
.fade-in {
  animation: fadeIn 0.5s ease forwards;
}

.slide-up {
  animation: slideUp 0.5s ease forwards;
}
```

### Animation Properties

```css
.element {
  animation: slideUp 0.5s ease forwards;
  animation-delay: 0.2s;
  animation-iteration-count: 1; /* or infinite */
  animation-direction: normal; /* normal, reverse, alternate */
  animation-fill-mode: forwards;  /* none, forwards, backwards, both */
}
```

## Animation Orchestration

### Staggered Delays

```css
.item {
  opacity: 0;
  animation: fadeIn 0.4s ease forwards;
}

.item:nth-child(1) { animation-delay: 0.1s; }
.item:nth-child(2) { animation-delay: 0.2s; }
.item:nth-child(3) { animation-delay: 0.3s; }
.item:nth-child(4) { animation-delay: 0.4s; }

/* Or with custom properties */
.item:nth-child(n) {
  animation-delay: calc((n - 1) * 0.1s);
}
```

### Entrance Animations

```css
@keyframes fadeSlideIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.hero-title {
  animation: fadeSlideIn 0.6s ease-out forwards;
}

.hero-subtitle {
  animation: fadeSlideIn 0.6s ease-out 0.2s forwards;
  opacity: 0;
}
```

## Transition Hints

### Will Change

```css
/* Hint browser to optimize */
.element {
  will-change: transform;
  transform: translateZ(0); /* Force GPU */
}
```

### Performance Tips

- **Prefer transform and opacity**: These don't trigger layout/paint
- **Avoid animating**: `width`, `height`, `top`, `left` (cause layout)
- **Use translateZ(0)**: Forces GPU layer

```css
.smooth {
  transform: translateZ(0);
  backface-visibility: hidden;
}
```

## JavaScript Animation Libraries (Integration)

### Framer Motion

```jsx
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5, delay: 0.2 }}
/>
```

### GSAP

```javascript
gsap.from('.element', {
  duration: 0.5,
  y: 20,
  opacity: 0,
  stagger: 0.1
});
```

### When to Use CSS vs JS

| CSS | JavaScript |
|-----|-----------|
| Simple transitions | Choreographed sequences |
| Hover/focus states | Scroll-triggered |
| Loading spinners | Complex physics |
| Toggle visibility | Timeline control |

## Common Patterns

### Toggle Switch

```css
.toggle {
  transition: background-color 0.2s;
}
.toggle[aria-checked="true"] {
  background-color: blue;
}
```

### Accordion

```css
.accordion-content {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.3s ease-out;
}
.accordion-content.open {
  max-height: 500px;
}
```

### Toast Notifications

```css
@keyframes slideInRight {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

.toast {
  animation: slideInRight 0.3s ease forwards;
}
```

## Accessibility

```css
/* Respect reduced motion */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```