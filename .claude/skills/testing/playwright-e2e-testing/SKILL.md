---
name: playwright-e2e-testing
description: Complete E2E testing and browser automation with Playwright. Build reliable test suites, automate browser tasks, test pages/forms, validate UX, check responsive design, test login flows, implement test patterns, debug flaky tests. Auto-detects dev servers, provides testing best practices. Use when implementing E2E tests, automating browser interactions, establishing testing standards, or debugging flaky tests.
version: 2.0.0
author: Claude Assistant
tags: [testing, automation, browser, e2e, playwright, web-testing, test-patterns]
---

**IMPORTANT - Path Resolution:**
This skill can be installed in different locations. Before executing any commands, determine the skill directory based on where you loaded this SKILL.md file. Replace `$SKILL_DIR` with the actual discovered path.

Common installation paths:
- Plugin system: `~/.claude/plugins/marketplaces/playwright-skill/skills/playwright-skill`
- Manual global: `~/.claude/skills/playwright-e2e-testing`
- Project-specific: `<project>/.claude/skills/playwright-e2e-testing`

# Playwright E2E Testing & Browser Automation

Build reliable, fast, maintainable end-to-end test suites with Playwright. Comprehensive patterns for E2E testing, browser automation, and test suite optimization.

## Level 1: Overview

Master end-to-end testing with Playwright - from automated browser tasks to production-grade test suites. Covers test patterns, automation workflows, debugging flaky tests, and CI/CD integration.

## Prerequisites

- Node.js installed
- Project with web application to test
- Basic understanding of testing concepts

## What This Skill Does

1. **Browser Automation** - Auto-detects dev servers, writes custom Playwright scripts
2. **E2E Test Patterns** - Page Object Model, fixtures, waiting strategies
3. **Testing Best Practices** - Test independence, data cleanup, reliable selectors
4. **Visual & Accessibility Testing** - Screenshots, visual regression, a11y checks
5. **Debug Flaky Tests** - Proper waits, error handling, debugging techniques
6. **CI/CD Integration** - Parallel testing, sharding, test reporting

---

## Level 2: Quick Start

### Setup (First Time)

```bash
cd $SKILL_DIR
npm run setup
```

Installs Playwright and Chromium browser. Only needed once.

### Critical Workflow - Follow These Steps

**Step 1: Auto-detect dev servers (for localhost testing)**

```bash
cd $SKILL_DIR && node -e "require('./lib/helpers').detectDevServers().then(s => console.log(JSON.stringify(s)))"
```

- If **1 server found**: Use it automatically
- If **multiple servers found**: Ask user which one
- If **no servers**: Ask for URL or help start server

**Step 2: Write test script to /tmp**

```javascript
// /tmp/playwright-test-example.js
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3001'; // Auto-detected or from user

(async () => {
  const browser = await chromium.launch({ headless: false });
  const page = await browser.newPage();

  await page.goto(TARGET_URL);
  console.log('Page title:', await page.title());

  await page.screenshot({ path: '/tmp/screenshot.png', fullPage: true });
  console.log('📸 Screenshot saved');

  await browser.close();
})();
```

**Step 3: Execute from skill directory**

```bash
cd $SKILL_DIR && node run.js /tmp/playwright-test-example.js
```

### Key Principles

- **ALWAYS detect servers first** for localhost testing
- **Write scripts to /tmp** (never skill directory or user's project)
- **Parameterize URLs** - `TARGET_URL` constant at top
- **Default: visible browser** - `headless: false` unless user requests headless
- **Slow down for visibility** - Use `slowMo: 100`
- **Proper waits** - Use `waitForURL`, `waitForSelector` not fixed timeouts

---

## E2E Testing Patterns

### Pattern 1: Page Object Model

Encapsulate page logic for maintainability and reusability.

```typescript
// pages/LoginPage.ts
import { Page, Locator } from '@playwright/test';

export class LoginPage {
    readonly page: Page;
    readonly emailInput: Locator;
    readonly passwordInput: Locator;
    readonly loginButton: Locator;
    readonly errorMessage: Locator;

    constructor(page: Page) {
        this.page = page;
        this.emailInput = page.getByLabel('Email');
        this.passwordInput = page.getByLabel('Password');
        this.loginButton = page.getByRole('button', { name: 'Login' });
        this.errorMessage = page.getByRole('alert');
    }

    async goto() {
        await this.page.goto('/login');
    }

    async login(email: string, password: string) {
        await this.emailInput.fill(email);
        await this.passwordInput.fill(password);
        await this.loginButton.click();
    }

    async getErrorMessage(): Promise<string> {
        return await this.errorMessage.textContent() ?? '';
    }
}

// Usage in test
import { test, expect } from '@playwright/test';
import { LoginPage } from './pages/LoginPage';

test('successful login', async ({ page }) => {
    const loginPage = new LoginPage(page);
    await loginPage.goto();
    await loginPage.login('user@example.com', 'password123');

    await expect(page).toHaveURL('/dashboard');
    await expect(page.getByRole('heading', { name: 'Dashboard' }))
        .toBeVisible();
});
```

### Pattern 2: Test Fixtures for Data Management

```typescript
// fixtures/test-data.ts
import { test as base } from '@playwright/test';

type TestData = {
    testUser: {
        email: string;
        password: string;
        name: string;
    };
};

export const test = base.extend<TestData>({
    testUser: async ({}, use) => {
        const user = {
            email: `test-${Date.now()}@example.com`,
            password: 'Test123!@#',
            name: 'Test User',
        };
        // Setup: Create user
        await createTestUser(user);
        await use(user);
        // Teardown: Clean up
        await deleteTestUser(user.email);
    },
});

// Usage
import { test } from './fixtures/test-data';

test('user can update profile', async ({ page, testUser }) => {
    // Auto setup/teardown, testUser ready to use
    await page.goto('/login');
    await page.getByLabel('Email').fill(testUser.email);
    await page.getByLabel('Password').fill(testUser.password);
    await page.getByRole('button', { name: 'Login' }).click();

    // ... test logic
});
```

### Pattern 3: Proper Waiting Strategies

```typescript
// ❌ BAD: Fixed timeouts (flaky!)
await page.waitForTimeout(3000);

// ✅ GOOD: Wait for specific conditions
await page.waitForLoadState('networkidle');
await page.waitForURL('/dashboard');
await page.waitForSelector('[data-testid="user-profile"]');

// ✅ BETTER: Auto-waiting with assertions
await expect(page.getByText('Welcome')).toBeVisible();
await expect(page.getByRole('button', { name: 'Submit' })).toBeEnabled();

// Wait for API response
const responsePromise = page.waitForResponse(
    response => response.url().includes('/api/users') && response.status() === 200
);
await page.getByRole('button', { name: 'Load Users' }).click();
const response = await responsePromise;
expect(await response.json()).toHaveProperty('users');

// Wait for multiple conditions
await Promise.all([
    page.waitForURL('/success'),
    page.waitForLoadState('networkidle'),
    expect(page.getByText('Payment successful')).toBeVisible(),
]);
```

### Pattern 4: Network Mocking & Interception

```typescript
// Mock API responses
test('displays error when API fails', async ({ page }) => {
    await page.route('**/api/users', route => {
        route.fulfill({
            status: 500,
            contentType: 'application/json',
            body: JSON.stringify({ error: 'Internal Server Error' }),
        });
    });

    await page.goto('/users');
    await expect(page.getByText('Failed to load users')).toBeVisible();
});

// Intercept and modify requests
await page.route('**/api/users', async route => {
    const request = route.request();
    const postData = JSON.parse(request.postData() || '{}');

    // Modify request
    postData.role = 'admin';

    await route.continue({
        postData: JSON.stringify(postData),
    });
});

// Mock third-party services
await page.route('**/api/stripe/**', route => {
    route.fulfill({
        status: 200,
        body: JSON.stringify({
            id: 'mock_payment_id',
            status: 'succeeded',
        }),
    });
});
```

---

## Browser Automation Patterns

### Quick Automation: Take Screenshots

```javascript
// /tmp/playwright-test-screenshot.js
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3001';

(async () => {
  const browser = await chromium.launch({ headless: false });
  const page = await browser.newPage();

  await page.goto(TARGET_URL);
  await page.screenshot({ path: '/tmp/page.png', fullPage: true });

  console.log('📸 Screenshot saved to /tmp/page.png');
  await browser.close();
})();
```

### Test Responsive Design

```javascript
// /tmp/playwright-test-responsive.js
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3001';

(async () => {
  const browser = await chromium.launch({ headless: false, slowMo: 100 });
  const page = await browser.newPage();

  const viewports = [
    { name: 'Desktop', width: 1920, height: 1080 },
    { name: 'Tablet', width: 768, height: 1024 },
    { name: 'Mobile', width: 375, height: 667 }
  ];

  for (const viewport of viewports) {
    console.log(`Testing ${viewport.name}`);
    await page.setViewportSize({ width: viewport.width, height: viewport.height });
    await page.goto(TARGET_URL);
    await page.screenshot({ path: `/tmp/${viewport.name.toLowerCase()}.png`, fullPage: true });
  }

  console.log('✅ All viewports tested');
  await browser.close();
})();
```

### Test Login Flow

```javascript
// /tmp/playwright-test-login.js
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3001';

(async () => {
  const browser = await chromium.launch({ headless: false });
  const page = await browser.newPage();

  await page.goto(`${TARGET_URL}/login`);

  await page.fill('input[name="email"]', 'test@example.com');
  await page.fill('input[name="password"]', 'password123');
  await page.click('button[type="submit"]');

  // Wait for redirect
  await page.waitForURL('**/dashboard');
  console.log('✅ Login successful, redirected to dashboard');

  await browser.close();
})();
```

### Fill and Submit Forms

```javascript
// /tmp/playwright-test-form.js
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3001';

(async () => {
  const browser = await chromium.launch({ headless: false, slowMo: 50 });
  const page = await browser.newPage();

  await page.goto(`${TARGET_URL}/contact`);

  await page.fill('input[name="name"]', 'John Doe');
  await page.fill('input[name="email"]', 'john@example.com');
  await page.fill('textarea[name="message"]', 'Test message from automation');
  await page.click('button[type="submit"]');

  // Verify submission
  await page.waitForSelector('.success-message');
  console.log('✅ Form submitted successfully');

  await browser.close();
})();
```

---

## Advanced Patterns

### Visual Regression Testing

```typescript
import { test, expect } from '@playwright/test';

test('homepage looks correct', async ({ page }) => {
    await page.goto('/');
    await expect(page).toHaveScreenshot('homepage.png', {
        fullPage: true,
        maxDiffPixels: 100,
    });
});

test('button in all states', async ({ page }) => {
    await page.goto('/components');
    const button = page.getByRole('button', { name: 'Submit' });

    // Default state
    await expect(button).toHaveScreenshot('button-default.png');

    // Hover state
    await button.hover();
    await expect(button).toHaveScreenshot('button-hover.png');

    // Disabled state
    await button.evaluate(el => el.setAttribute('disabled', 'true'));
    await expect(button).toHaveScreenshot('button-disabled.png');
});
```

### Accessibility Testing

```typescript
// Install: npm install @axe-core/playwright
import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

test('page should not have accessibility violations', async ({ page }) => {
    await page.goto('/');

    const accessibilityScanResults = await new AxeBuilder({ page })
        .exclude('#third-party-widget')
        .analyze();

    expect(accessibilityScanResults.violations).toEqual([]);
});

test('form is accessible', async ({ page }) => {
    await page.goto('/signup');

    const results = await new AxeBuilder({ page })
        .include('form')
        .analyze();

    expect(results.violations).toEqual([]);
});
```

### Parallel Testing with Sharding

```typescript
// playwright.config.ts
export default defineConfig({
    projects: [
        {
            name: 'shard-1',
            use: { ...devices['Desktop Chrome'] },
            shard: { current: 1, total: 4 },
        },
        {
            name: 'shard-2',
            use: { ...devices['Desktop Chrome'] },
            shard: { current: 2, total: 4 },
        },
        // ... more shards
    ],
});

// Run in CI
// npx playwright test --shard=1/4
// npx playwright test --shard=2/4
```

---

## Common Use Cases

### Case 1: Test Marketing Page UX
Auto-detect dev server, test page across Desktop/Tablet/Mobile viewports, take screenshots, verify responsive design.

### Case 2: Validate Login/Signup Flow
Test complete authentication flow, verify redirects, check error messages, ensure session persistence.

### Case 3: E2E Checkout Testing
Test entire checkout process: add to cart, apply coupon, fill shipping, process payment, verify order confirmation.

### Case 4: Debug Flaky Test
Identify race conditions, add proper waits, mock unstable APIs, verify deterministic behavior.

### Case 5: CI/CD Integration
Set up parallel test execution, configure sharding, implement retry logic, generate test reports.

---

## Best Practices

### Selector Strategies
```typescript
// ❌ BAD: Brittle selectors
cy.get('.btn.btn-primary.submit-button').click();
cy.get('div > form > div:nth-child(2) > input').type('text');

// ✅ GOOD: Semantic selectors
page.getByRole('button', { name: 'Submit' }).click();
page.getByLabel('Email address').fill('user@example.com');
page.locator('[data-testid="email-input"]').fill('user@example.com');
```

### Test Independence
- Each test should run in isolation
- Create and destroy test data in each test
- Don't rely on execution order
- Use fixtures for setup/teardown

### Optimization
- Mock external APIs when possible
- Use parallel execution (`fullyParallel: true`)
- Implement test sharding for large suites
- Only test critical paths with E2E (use unit tests for details)

---

## Available Helpers

Optional utility functions in `lib/helpers.js`:

```javascript
const helpers = require('./lib/helpers');

// Detect running dev servers (CRITICAL - use first!)
const servers = await helpers.detectDevServers();

// Safe click with retry
await helpers.safeClick(page, 'button.submit', { retries: 3 });

// Safe type with clear
await helpers.safeType(page, '#username', 'testuser');

// Take timestamped screenshot
await helpers.takeScreenshot(page, 'test-result');

// Handle cookie banners
await helpers.handleCookieBanner(page);

// Extract table data
const data = await helpers.extractTableData(page, 'table.results');
```

---

## Debugging Failing Tests

```typescript
// 1. Run in headed mode
npx playwright test --headed

// 2. Run in debug mode
npx playwright test --debug

// 3. Use trace viewer
await page.screenshot({ path: 'screenshot.png' });
await page.video()?.saveAs('video.webm');

// 4. Add test.step for better reporting
test('checkout flow', async ({ page }) => {
    await test.step('Add item to cart', async () => {
        await page.goto('/products');
        await page.getByRole('button', { name: 'Add to Cart' }).click();
    });

    await test.step('Proceed to checkout', async () => {
        await page.goto('/cart');
        await page.getByRole('button', { name: 'Checkout' }).click();
    });
});

// 5. Inspect page state
await page.pause();  // Opens Playwright Inspector
```

---

## Common Pitfalls & Solutions

| Problem | Cause | Solution |
|---------|-------|----------|
| Flaky tests | Fixed timeouts | Use waitForSelector, waitForURL, auto-waiting assertions |
| Slow tests | No mocking | Mock external APIs, use parallel execution |
| Over-testing | Testing every edge case | E2E for critical paths only, unit tests for details |
| Coupled tests | Tests depend on order | Make tests independent, use fixtures |
| Poor selectors | CSS classes, nth-child | Use data-testid, semantic roles, labels |
| No cleanup | Test data persists | Implement teardown in fixtures |
| Testing implementation | Checking internal details | Test user behavior, not code structure |

---

## Troubleshooting

**Playwright not installed:**
```bash
cd $SKILL_DIR && npm run setup
```

**Module not found:**
Ensure running from skill directory via `run.js` wrapper

**Browser doesn't open:**
Check `headless: false` and ensure display available

**Element not found:**
```javascript
await page.waitForSelector('.element', { timeout: 10000 });
```

**Test times out:**
- Check network requests (use DevTools)
- Verify proper waits, not fixed timeouts
- Mock slow external APIs

---

## Integration & CI/CD

### CI Configuration Example

```yaml
# .github/workflows/e2e-tests.yml
name: E2E Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        shard: [1, 2, 3, 4]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npx playwright test --shard=${{ matrix.shard }}/4
      - uses: actions/upload-artifact@v3
        if: always()
        with:
          name: playwright-report-${{ matrix.shard }}
          path: playwright-report/
```

---

## Summary

This skill combines:
- **Browser Automation** - Quick, flexible automation for any browser task
- **E2E Test Patterns** - Production-grade test suites with best practices
- **Debugging Tools** - Identify and fix flaky tests
- **CI/CD Integration** - Scale tests across multiple machines

**Result**: Build reliable, fast test suites that catch bugs before users and enable confident, rapid deployment.
