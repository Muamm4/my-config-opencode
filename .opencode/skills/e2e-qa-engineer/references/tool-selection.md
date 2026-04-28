# Tool Selection for E2E Testing

**Purpose**: Guide selection of the appropriate E2E testing framework based on project requirements.

## Decision Framework

| Factor | Playwright | Cypress | Puppeteer | TestCafé |
|--------|------------|---------|-----------|----------|
| **Best For** | Modern web apps, cross-browser | Developer experience, debugging | Scraping, Chrome-only | No-setup, legacy apps |
| **Browser Support** | All major | Chromium, Firefox, WebKit | Chromium | All major |
| **Language** | TypeScript, JavaScript | JavaScript, TypeScript | JavaScript | JavaScript, TypeScript |
| **Setup Complexity** | Low | Low | Medium | Very Low |
| **Debugging** | Excellent (UI mode, traces) | Excellent (UI, snapshots) | Good | Good |
| **API Flakiness** | Low | Medium | Low | Medium |

## Recommended Stack

**Default Choice**: Playwright
- Modern architecture (independent browser contexts)
- Native wait handling
- Excellent tracing for debugging
- Strong TypeScript support

**For Legacy Teams**: Cypress
- Lower learning curve
- Extensive documentation
- Rich ecosystem

## Project Initialization

```bash
# Playwright (Recommended)
npm init playwright@latest
# or
npx playwright install --with-deps

# Cypress
npm install cypress --save-dev
npx cypress install
```

## Configuration Patterns

### Playwright config.ts
```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
    { name: 'webkit', use: { ...devices['Desktop Safari'] } },
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

### Cypress cypress.config.ts
```typescript
import { defineConfig } from 'cypress';

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    video: false,
    screenshotOnRunFailure: true,
    setupNodeEvents() {
      // implement node event listeners here
    },
  },
});
```

## Key Selection Criteria

1. **Team Experience**: Cypress has gentler learning curve
2. **Browser Requirements**: Playwright for multi-browser; Cypress limits parallelization
3. **Test Complexity**: Playwright for complex auth flows and iframes
4. **CI/CD Needs**: Playwright better Docker support
5. **Legacy Apps**: TestCafé for older Angular/jQuery apps

## Environment-Specific Recommendations

| Environment | Recommended |
|-------------|-------------|
| Laravel + Inertia | Playwright |
| Next.js/React | Playwright |
| Vue 2/jQuery | Cypress or TestCafé |
| Electron apps | Playwright |
| Mobile-first PWA | Playwright (device emulation) |