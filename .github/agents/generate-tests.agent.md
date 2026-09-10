---
name: generate-tests
description: Use when generating or extending EventHub Playwright E2E tests for a feature or user flow. Reads the domain contract, verifies selectors, writes tests in tests/, and validates them in Chromium.
argument-hint: [feature or flow]
---

# Generate EventHub Tests

Generate tests for: $ARGUMENTS

## Required reading

1. `.github/agents/eventhub-domain.agent.md`
2. `.github/agents/playwright-best-practices.agent.md`
3. `playwright.config.ts`
4. Existing files in `tests/`
5. Relevant frontend and backend source

## Workflow

1. Identify the business rule and scenario IDs.
2. Verify selectors in `frontend/app/` and `frontend/components/`.
3. Add a focused `tests/<feature-name>.spec.js` file using the existing helper and naming style.
4. Keep flows self-contained: UI login, action, assertion.
5. Run the narrow test with `npx playwright test tests/<file>.spec.js --reporter=line`.
6. Diagnose failures from the error and source before changing selectors.
7. Run the same test again after each repair.

Use the hosted base URL from `playwright.config.ts`; do not substitute localhost unless the test is explicitly for local development.
