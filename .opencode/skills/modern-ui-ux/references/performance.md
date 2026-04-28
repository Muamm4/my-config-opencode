# UI Performance & Perceived Speed

Optimizing the technical and psychological aspects of interface speed to ensure a fluid user experience.

## Core Web Vitals (CWV)

Google's standardized metrics for measuring the real-world user experience of a page's loading performance.

### 1. LCP (Largest Contentful Paint)
- **What it is**: The time it takes for the largest image or text block to become visible.
- **Goal**: < 2.5 seconds.
- **Optimization:**
  - Optimize and compress images (WebP, AVIF).
  - Use `priority` hints for LCP images (`fetchpriority="high"`).
  - Reduce server response time (TTFB).
  - Avoid render-blocking JavaScript and CSS.

### 2. CLS (Cumulative Layout Shift)
- **What it is**: The amount of unexpected layout shift that occurs during the entire lifespan of the page.
- **Goal**: < 0.1.
- **Optimization:**
  - Always include `width` and `height` attributes on images and videos.
  - Reserve space for ad slots or dynamic content using placeholders.
  - Avoid inserting new content above existing content (except in response to user interaction).
  - Use `font-display: swap` to prevent layout shifts during font loading.

### 3. INP (Interaction to Next Paint)
- **What it is**: Measures the latency of all interactions (clicks, taps, keyboard presses) and reports the longest one.
- **Goal**: < 200 milliseconds.
- **Optimization:**
  - Break up long tasks in the main thread using `setTimeout` or `requestIdleCallback`.
  - Use Web Workers for heavy computations.
  - Optimize event handlers to be lean and fast.
  - Avoid synchronous XHR or heavy DOM manipulations during interaction.

---

## Perceived Performance

The psychological perception of speed, which is often more important than the actual technical load time.

### 1. Skeleton Screens
Instead of a generic loading spinner, show a simplified version of the page layout that "shimmers" while content loads.
- **Why**: It gives the user a sense of progress and prepares them for where the content will appear.
- **Implementation**: Use gray blocks with a linear-gradient animation.

### 2. Optimistic UI Updates
Update the UI immediately when a user performs an action, assuming the server request will succeed.
- **Why**: Eliminates the perceived wait time for network round-trips.
- **Implementation**:
  - User clicks "Like" $\rightarrow$ Heart turns red immediately.
  - Send request to server in background.
  - If request fails $\rightarrow$ Revert the UI state and show an error toast.

### 3. Staggered Loading (Progressive Loading)
Load critical content first, then load secondary content in waves.
- **Why**: Reduces the initial load burden and makes the page feel interactive sooner.
- **Implementation**:
  - Priority 1: Above-the-fold text and LCP image.
  - Priority 2: Interactive elements and primary navigation.
  - Priority 3: Footer, related content, and heavy images below the fold.

### 4. Anticipatory Design
Predict the user's next move and start loading the data before they even click.
- **Why**: Makes transitions feel instantaneous.
- **Implementation**:
  - Pre-fetch data when the user hovers over a link.
  - Use `<link rel="prefetch">` for likely next pages.

## Performance Verification Checklist

- [ ] LCP is under 2.5s on 4G/Slow 3G connections.
- [ ] No unexpected layout shifts (CLS < 0.1) during loading.
- [ ] All primary interactions respond in < 200ms (INP).
- [ ] Skeleton screens are used for all asynchronous content loads.
- [ ] Optimistic UI is applied to high-frequency actions (likes, toggles, saves).
- [ ] Critical CSS is inlined to prevent render-blocking.
- [ ] Images are served in modern formats (WebP/AVIF) with responsive sizes.
