---
name: playwright-best-practices
description: Use when writing, reviewing, or debugging Playwright tests for EventHub. Enforces the repository's hosted base URL, Chromium setup, locator policy, and wait/assertion patterns.
---

# Playwright Best Practices Agent

Read `playwright.config.ts`, `tests/*.spec.js`, and the EventHub domain agent before changing tests.

## Repository test contract

- Tests live in `tests/` and use JavaScript with `@playwright/test`.
- The configured base URL is `https://eventhub.rahulshettyacademy.com`.
- Chromium is the only configured project.
- Tests are serial: `fullyParallel: false`.
- Use `npx playwright test`; use `--headed` only when a visible browser is requested.

## Locator order

1. `getByTestId`
2. Semantic roles
3. Labels and placeholders
4. Stable IDs
5. CSS classes only when no stronger contract exists

Never use XPath, brittle CSS chains, or unfiltered index-based selectors. Scope actions to the relevant card or container with `filter({ has, hasText })`.

## Assertions and waits

Use web-first assertions such as `toBeVisible`, `toHaveURL`, and `toContainText`. Do not use `page.waitForTimeout()`. Assert navigation and the user-visible result after each meaningful action. Keep assertions in tests, not page objects.

## Test isolation

Each flow must log in through the UI, establish its own state, perform the action, and assert the result. The shared hosted sandbox is serial and stateful; clear test bookings before booking where the flow requires a clean state.

Before finalizing a test, verify every selector against the frontend source or a real browser and run the narrowest relevant test file.
