# UI/UX Workflow

A systematic approach to designing user interfaces that prioritize user needs, engineering scalability, and perceived performance.

## The Four-Phase Workflow

### Phase 1: User Research & Flow Analysis

**Objective**: Understand the user's mental model and map their journey before designing the interface.

**Activities:**
1. **User Research**:
   - **Personas**: Create detailed user personas (goals, frustrations, technical proficiency).
   - **Jobs-to-be-Done (JTBD)**: Define the "job" the user is hiring the product for (e.g., "When I [situation], I want to [motivation], so that I can [expected outcome]").
2. **Flow Mapping**:
   - Identify the primary user goal and the "happy path" (ideal journey).
   - Map alternative paths (error states, edge cases, back navigation).
   - Identify decision points (where users make choices) and friction points (where users might drop off).
3. **Requirement Synthesis**: Translate research and flows into a list of essential functional requirements for the UI.

**Output:**
- User Personas and JTBD Framework
- User flow diagrams or written journeys
- Lists of primary, secondary, and error paths
- Identified friction points and decision nodes

**Questions to Answer:**
- Who is the user and what is their core motivation?
- What is the "job" they are trying to get done?
- What's the minimum number of steps to achieve the goal?
- What could go wrong, and how do they recover?

### Phase 2: Wireframing & Information Architecture

**Objective**: Define the structural foundation, content hierarchy, and scalability of the interface.

**Activities:**
1. **Information Architecture**: Organize content logically to minimize cognitive load.
2. **Low-Fidelity Wireframes**: Create sketches or basic shapes to define layout without visual distraction.
3. **Content Hierarchy**: Establish what's most important on each screen (Visual Anchor points).
4. **Interactive Mapping**: Map every interactive element to a specific user goal identified in Phase 1.
5. **Scalability Check**: Ensure the layout can handle varying content lengths and future feature additions.

**Principles:**
- **Hierarchy First**: The most important element should be most visible.
- **Fitts's Law**: Make important actions easy to reach/tap.
- **Jakob's Law**: Follow familiar patterns from similar apps.
- **Grouping**: Related elements belong together (Gestalt).

**Output:**
- Digital or hand-drawn wireframes
- Content priority map
- Interactive element-to-goal mapping
- Basic IA site map or screen map

**Questions to Answer:**
- Is the most important action obvious?
- Can users find their way back if they navigate away?
- Are related items grouped logically?
- Will this layout break if the content doubles in size?

### Phase 3: Visual Polish & Design-to-Code

**Objective**: Apply aesthetics and engineering patterns to create a maintainable, high-fidelity interface.

**Activities:**
1. **Design Token Application**: Apply spacing, colors, and typography from the design system.
2. **Design-to-Code Patterns**:
   - **Atomic Design**: Break UI into Atoms (buttons), Molecules (search bars), Organisms (headers), Templates, and Pages.
   - **Component Scalability**: Define component variants (size, state, intent) to avoid "prop drilling" or CSS bloat.
3. **Visual Feedback**: Implement micro-interactions and state changes (hover, focus, active, disabled).
4. **Visual Hierarchy**: Use sizing, weight, and color to guide the user's eye toward the primary CTA.
5. **Polish**: Refine borders, shadows, and spacing for a professional, modern feel.

**Principles:**
- **Whitespace**: Generous space reduces cognitive load.
- **Typography**: Size and weight create hierarchy, not arbitrary variety.
- **Color**: Use sparingly for meaning, not decoration.
- **Consistency**: Same elements must look and behave the same way.

**Output:**
- High-fidelity styled mockups
- Component library/specification (Atoms, Molecules, Organisms)
- Defined interaction states and transition timings

**Questions to Answer:**
- Does the design "breathe"?
- Are interactive elements clearly identifiable?
- Is the visual hierarchy clear without reading?
- Is the component structure scalable and maintainable for developers?

### Phase 4: Verification & Performance

**Objective**: Validate the interface against accessibility standards, usability heuristics, and performance metrics.

**Activities:**
1. **Accessibility Audit**:
   - Run automated tests (axe-core, Lighthouse).
   - Verify keyboard navigation (tab order, focus states) and screen reader compatibility.
   - Check color contrast ratios (WCAG AA).
2. **Usability Evaluation**: Conduct heuristic evaluation against UX psychology laws.
3. **Performance Verification**:
   - **Core Web Vitals**: Measure LCP (Largest Contentful Paint), CLS (Cumulative Layout Shift), and INP (Interaction to Next Paint).
   - **Perceived Performance**: Implement skeleton screens, optimistic UI updates, and staggered loading to make the app *feel* faster.
4. **Recovery Testing**: Ensure every error state has a clear, helpful recovery path.

**Checklist:**
- [ ] All interactive elements keyboard accessible
- [ ] Focus states visible and logical
- [ ] Color contrast meets WCAG AA (4.5:1 for text)
- [ ] LCP < 2.5s, CLS < 0.1, INP < 200ms
- [ ] Skeleton screens used for slow-loading content
- [ ] Optimistic UI applied to primary actions
- [ ] User flow can be completed without mouse
- [ ] No fatal error states without recovery path

**Questions to Answer:**
- Can this interface be used by someone with visual or motor impairments?
- Does the interface feel responsive and fast, regardless of actual load time?
- Can users recover from errors without frustration?

## Workflow Summary

| Phase | Focus | Key Input | Key Output |
|-------|-------|-----------|------------|
| 1. Research & Flow | User Needs & Journey | User Goals | Personas, JTBD, Flow Map |
| 2. Wireframe | Structure & IA | Flow Map | Wireframes, IA Map |
| 3. Visual Polish | Aesthetics & Engineering | Wireframes | High-Fi Mockups, Component Lib |
| 4. Verification | a11y, Usability, Perf | High-Fi Mockups | Verified & Optimized UI |

## Key Principles

1. **User-First**: Always start with user needs, not visual preferences.
2. **Engineering-Aware**: Design for maintainability (Atomic Design) and performance (Core Web Vitals).
3. **Iteration**: Each phase may reveal issues requiring earlier phases.
4. **Progressive Disclosure**: Show complexity only when needed.
