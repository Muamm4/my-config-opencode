# Tailwind CSS Mastery

Expertise in Tailwind CSS, JIT mode, and custom configuration.

## Core Concepts

### Utility-First Approach

```html
<!-- Compose complex UIs from utilities -->
<button class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition">
  Click Me
</button>
```

### JIT Mode (Always On)

Tailwind v3+ uses JIT by default - generates only used styles.

```css
/* Arbitrary values */
<div class="w-[300px] bg-[#ff0000]">Custom</div>

/* Arbitrary variants */
<div class="[&:hover]:bg-red-500">Hover variant</div>
```

## Common Patterns

### Responsive Design

```html
<!-- Mobile-first: base class, then md:, lg: -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
  <!-- content -->
</div>
```

### Dark Mode

```html
<!-- strategy: 'class' in config -->
<div class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  <!-- content -->
</div>
```

### States

```html
<button class="hover:bg-blue-600 focus:ring-2 focus:ring-blue-300 disabled:opacity-50">
  Button
</button>

<input class="focus:outline-none focus:ring-2" />

<!-- Group states -->
<div class="group hover:bg-gray-100">
  <p class="group-hover:text-blue-500">Text</p>
</div>
```

### Container Queries

```html
<div class="@container">
  <div class="@lg:flex @lg:items-center">
    <!-- content -->
  </div>
</div>
```

## Custom Configuration

### tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{html,js}"],
  theme: {
    extend: {
      colors: {
        primary: '#3b82f6',
        surface: '#ffffff',
      },
      spacing: {
        '128': '32rem',
      },
      borderRadius: {
        'xl': '1rem',
      },
    },
  },
  plugins: [],
}
```

### Extending Colors

```javascript
// In config
colors: {
  brand: {
    50: '#eff6ff',
    100: '#dbeafe',
    500: '#3b82f6',
    900: '#1e3a8a',
  }
}

// Usage
<div class="bg-brand-500">Brand</div>
```

### Custom Utilities

```javascript
// plugin
plugin(({ addUtilities }) => {
  addUtilities({
    '.text-balance': {
      'text-wrap': 'balance',
    },
  })
})
```

### Component Extraction

```css
/* Base component */
@layer components {
  .btn {
    @apply px-4 py-2 rounded-lg font-medium transition-colors;
  }
  
  .btn-primary {
    @apply bg-blue-500 text-white hover:bg-blue-600;
  }
  
  .card {
    @apply bg-white rounded-xl shadow-sm p-6;
  }
}
```

## Advanced Patterns

### Arbitrary Values

```html
<!-- Any value in brackets -->
<div class="top-[100vh] w-[calc(100%-2rem)] z-[9999]">
```

### Dynamic Values

```javascript
// Using CSS variables
<div style="--index: 5" class="translate-y-[calc(var(--index)*100%)]">
```

### Complex Selectors

```html
<!-- Peer (sibling states) -->
<div class="peer-checked:bg-blue-500">
<div class="peer-required:after:content-['*'] peer-required:after:text-red-500">
```

### Grid / Flex Patterns

```html
<!-- Holy Grail -->
<div class="grid grid-cols-[auto_1fr_auto] h-screen">
  <nav>Sidebar</nav>
  <main>Content</main>
  <aside>Widget</aside>
</div>

<!-- Auto-fit grid -->
<div class="grid grid-cols-[repeat(auto-fit,minmax(250px,1fr))] gap-4">
```

### Using @apply Wisely

```css
/* Good: repeated utility groups */
@layer components {
  .btn-primary {
    @apply bg-blue-500 text-white px-4 py-2 rounded-lg hover:bg-blue-600 transition-colors;
  }
}

/* Avoid: over-abstracting */
@layer utilities {
  /* Only when truly reusable */
}
```

## Common Pitfalls

1. **JIT not generating**: Check `content` paths in config
2. **Custom values not working**: Use bracket notation `w-[100px]`
3. **Styles not applying**: Check layer order (@base, @components, @utilities)
4. **Preflight overriding**: Use `@layer base` to modify
5. **Missing purging**: Ensure content paths correct in production config