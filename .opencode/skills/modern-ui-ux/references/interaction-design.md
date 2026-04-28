# Interaction Design

Crafting meaningful micro-interactions and feedback loops that enhance user experience.

## What Are Micro-interactions?

Micro-interactions are small, focused moments of interaction around a single task:
- Toggling a switch
- Liking a post
- Submitting a form
- Loading content
- Confirming an action

## Core Principles

### 1. Feedback (Provide Response)
Every user action must have a visible response:
- **Button press**: Visual state change (scale down, color shift)
- **Form submission**: Loading spinner or progress indicator
- **Success action**: Checkmark animation, toast notification
- **Error state**: Clear error message with guidance

### 2. State Changes (Visual Feedback)
Show clear states for all interactive elements:
- **Default**: Normal appearance
- **Hover**: Subtle highlight (background shift, slight scale)
- **Focus**: Visible focus ring for keyboard navigation
- **Active/Pressed**: Depressed appearance (scale down, darker shade)
- **Disabled**: Reduced opacity, no pointer events

### 3. Timing (Responsiveness)
- **Response time**: Instant feedback (< 100ms) for direct manipulation
- **Loading**: Show loading state within 100ms, complete within 3 seconds
- **Transitions**: Keep animations under 300ms, use easing curves
- **Delays**: Never leave users wondering if their action worked

### 4. Meaningful Motion
- **Purpose**: Every animation should communicate state change
- **Direction**: Motion should follow natural direction (items enter from where they appear)
- **Hierarchy**: More important = more prominent animation
- **Avoid**: Decorative animations that don't inform state

## Implementation Patterns

### Button Press
```css
.btn {
  transition: transform 150ms ease-out, background-color 150ms ease-out;
}
.btn:active {
  transform: scale(0.96);
}
```

### Loading State
```html
<!-- Show spinner within 100ms of action -->
<button class="btn" disabled>
  <span class="spinner"></span>
  Loading...
</button>
```

### Success Feedback
```html
<div class="toast toast-success">
  <svg class="checkmark">...</svg>
  Item saved successfully
</div>
```

## Best Practices

1. **Immediate Feedback**: User should never wonder if their action registered
2. **Consistent Timing**: Same action = same timing across the app
3. **Respect User Preference**: Support `prefers-reduced-motion`
4. **Accessible Feedback**: Ensure states are visible without relying solely on color