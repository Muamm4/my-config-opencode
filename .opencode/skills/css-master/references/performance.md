# CSS Performance

Optimizing for Core Web Vitals and rendering performance.

## Minimizing Layout Shifts (CLS)

### Avoiding CLS

```css
/* Always set dimensions on images/videos */
img, video {
  aspect-ratio: 16 / 9;
  width: 100%;
  height: auto;
}

/* Reserve space for dynamic content */
.ad-banner {
  min-height: 250px; /* Reserve space */
}

/* Use contain for elements with known size */
.widget {
  contain: content;
}
```

### Font Loading

```css
/* Preload and display swap */
@font-face {
  font-family: 'Custom';
  src: url('/fonts/custom.woff2') format('woff2');
  font-display: swap;
}

/* Fallback */
body {
  font-family: system-ui, sans-serif; /* Matches metrics */
}
```

### Dynamic Content

```css
/* Skeleton loading states */
.skeleton {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: skeleton-loading 1.5s infinite;
}
```

## Critical CSS

### Inline Critical Styles

```html
<head>
  <style>
    /* Critical above-the-fold styles only */
    .header { display: flex; align-items: center; }
    .hero { min-height: 100vh; }
    .btn { background: blue; }
  </style>
  <!-- Non-critical loaded async -->
  <link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
</head>
```

### CSS Extraction (Build Tools)

```javascript
// Using PostCSS or build tools
// Extract critical styles automatically
// Inline < 10KB, lazy load rest
```

### Code Splitting

```css
/* Split by route or component */
<link rel="stylesheet" href="/pages/home.css">
<link rel="stylesheet" href="/pages/about.css">

/* Or use media queries for conditional */
<link rel="stylesheet" href="print.css" media="print">
```

## Reducing Paint Times

### Layers and Compositing

```css
/* Promote to own layer */
.will-animate {
  transform: translateZ(0);
  will-change: transform;
}

/* Avoid paint triggers */
.no-paint {
  transform: translateZ(0);
  /* vs opacity, filter, box-shadow (cause repaint) */
}
```

### Containment

```css
/* Isolate from layout changes */
.component {
  contain: content;
  /* 'layout' - internal layout doesn't affect outside */
  /* 'paint' - can't paint outside */
  /* 'strict' - all of above */
}
```

### GPU Acceleration

```css
/* Force GPU */
.gpu {
  transform: translateZ(0);
  backface-visibility: hidden;
}

/* 3D transforms */
.perspective {
  transform: perspective(1000px) rotateY(5deg);
}
```

## Optimization Techniques

### Reduce Selectors

```css
/* Bad - overly specific */
.container aside ul li a span { }

/* Good - flat and direct */
.card-link { }
```

### Use Efficient Properties

| Efficient | Less Efficient |
|----------|--------------|
| transform | width, height, top |
| opacity | background-color |
| filter | box-shadow (sometimes) |

### Avoid Expensive Properties

```css
/* These cause layout/paint */
.property {
  border-radius: 4px;     /* Paint */
  box-shadow: 0 2px 4px; /* Paint */
  filter: blur(1px);       /* Paint */
}
```

### Use `content-visibility`

```css
/* Skip rendering off-screen content */
.lazy-section {
  content-visibility: auto;
  contain-intrinsic-size: 500px;
}
```

## Loading Performance

### Preload Resources

```html
<head>
  <link rel="preload" href="styles.css" as="style">
  <link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>
</head>
```

### Resource Hints

```html
<head>
  <!-- Prefetch next page -->
  <link rel="prefetch" href="/next-page.css">
  
  <!-- Preconnect to origin -->
  <link rel="preconnect" href="https://cdn.example.com">
</head>
```

### Media Attributes

```html
<!-- Async CSS -->
<link rel="stylesheet" href="mobile.css" media="screen and (max-width: 767px)">
<link rel="stylesheet" href="desktop.css" media="screen and (min-width: 768px)">
```

## Measuring Performance

### Chrome DevTools

1. **Lighthouse**: Run audit for CLS, LCP, FID
2. **Performance Tab**: Record and analyze frames
3. **Layers Panel**: Visualize compositing layers

### Key Metrics

| Metric | Target | What to Optimize |
|--------|-------|-----------------|
| LCP | < 2.5s | Critical CSS, font loading, hero image |
| FID | < 100ms | JavaScript execution time |
| CLS | < 0.1 | Reserve space, font fallbacks |

## Common Issues

1. **Animated width/height**: Use transform instead
2. **Animating top/left**: Use transform
3. **Box-shadow on scroll**: Will-change or avoid
4. **Large background images**: Optimize, lazy load
5. **Too many fonts**: Subset, preload only needed