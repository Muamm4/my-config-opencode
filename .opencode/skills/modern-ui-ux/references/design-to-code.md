# Design-to-Code Patterns

Bridging the gap between visual design and scalable engineering to ensure maintainable and consistent interfaces.

## 1. Atomic Design Methodology

A hierarchical system for organizing UI elements, moving from the smallest possible unit to full pages.

### Atoms
The smallest building blocks. They cannot be broken down further without losing their function.
- **Examples**: Buttons, Inputs, Labels, Icons, Typography styles, Color tokens.
- **Rule**: Atoms should be generic and highly reusable.

### Molecules
Groups of atoms bonded together to function as a unit.
- **Examples**: A search bar (Input + Button + Label), a form field (Label + Input + Error Message).
- **Rule**: Molecules should do one thing well.

### Organisms
Complex UI components composed of molecules and/or atoms. These form distinct sections of an interface.
- **Examples**: Header (Logo + Nav + Search Bar), Product Card (Image + Title + Price + Add to Cart Button), Footer.
- **Rule**: Organisms are often the primary units of layout.

### Templates
Page-level objects that place components into a layout. They focus on the structure rather than the final content.
- **Examples**: Dashboard Layout, Article Page Template, User Profile Template.
- **Rule**: Templates define the "skeleton" of the page.

### Pages
Specific instances of templates that are populated with real content.
- **Examples**: "My Account" page, "Home" page, "Product Detail" page for a specific item.
- **Rule**: Pages are used to test the effectiveness of the design with real data.

---

## 2. Component Scalability & Engineering

Ensuring that components remain maintainable as the project grows.

### Variant-Based Architecture
Avoid creating separate components for every slight variation. Instead, use a variant system.
- **Intent**: `primary`, `secondary`, `danger`, `success`, `ghost`.
- **Size**: `sm`, `md`, `lg`, `xl`.
- **State**: `default`, `hover`, `active`, `disabled`, `loading`.
- **Example**: Instead of `PrimaryButton` and `DangerButton`, use `Button` with `intent="primary"` or `intent="danger"`.

### Prop-Driven Configuration
Define a clear API for components to prevent "prop drilling" and maintain a clean interface.
- **Prefer Composition**: Use `children` or slots for flexible content instead of passing dozens of strings as props.
- **Strict Typing**: Use TypeScript interfaces to define exactly what a component accepts.

### The "Single Source of Truth" (SSOT)
All visual constants must come from the design system tokens, never hardcoded.
- **Wrong**: `color: #3b82f6`
- **Right**: `color: var(--color-primary-500)`

---

## 3. Implementation Workflow

How to translate a design into a scalable codebase:

1. **Audit the Design**: Identify all repeating patterns and elements.
2. **Extract Atoms**: Define the base tokens (colors, spacing, typography) and basic elements.
3. **Build Molecules**: Combine atoms into functional units.
4. **Assemble Organisms**: Create the larger sections of the page.
5. **Apply Templates**: Map organisms into the overall page layout.
6. **Populate Pages**: Fill the templates with real content and verify the design.

## Design-to-Code Checklist

- [ ] UI is broken down into Atoms, Molecules, and Organisms.
- [ ] Components use a variant system (intent, size, state) instead of duplication.
- [ ] No hardcoded visual values; all use design tokens.
- [ ] Component API is lean and uses composition where possible.
- [ ] Layouts are template-based and handle dynamic content lengths.
- [ ] All interactive states (hover, focus, active) are implemented.
