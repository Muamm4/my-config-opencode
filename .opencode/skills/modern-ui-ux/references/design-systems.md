# Design Systems

Design systems provide the foundational building blocks for consistent, scalable interfaces.

## Core Components

### 1. Spacing Scale
- Use a consistent spacing scale (e.g., 4px base: 4, 8, 12, 16, 24, 32, 48, 64, 96)
- Apply spacing logically: small gaps between related elements, larger gaps between sections
- Key utility classes: `.gap-2`, `.gap-4`, `.gap-6`, `.gap-8`, `.p-4`, `.m-4`

### 2. Typography Scale
- Define font sizes hierarchically: display / heading-1 / heading-2 / heading-3 / body / small / caption
- Establish line height ratios: tighter for headings (1.1-1.2), relaxed for body (1.5-1.6)
- Limit font weights: regular (400) for body, semibold (600) or bold (700) for emphasis
- Key utility classes: `.text-xs`, `.text-sm`, `.text-base`, `.text-lg`, `.text-xl`, `.text-2xl`, `.font-normal`, `.font-medium`, `.font-bold`

### 3. Color Palette
- **Semantic colors**: primary, secondary, success, warning, error, neutral
- **Shading**: light-100 through light-900 / dark-100 through dark-900
- Ensure sufficient contrast ratios: 4.5:1 for normal text, 3:1 for large text
- Key utility classes: `.text-primary`, `.bg-primary`, `.ring-primary`, border colors with `.border-*`

### 4. Border Radius
- Consistent border radius: none (0), sm (4px), base (6px), md (8px), lg (12px), xl (16px), full (9999)
- Key utility classes: `.rounded-none`, `.rounded-sm`, `.rounded`, `.rounded-md`, `.rounded-lg`, `.rounded-full`

## Best Practices

1. **Token-Based**: Always use design tokens (CSS variables or utility classes), never hardcode values
2. **Consistency First**: Prefer existing tokens over introducing new ones
3. **Scale Rationale**: Ensure the scale makes sense semantically (not arbitrary numbers)