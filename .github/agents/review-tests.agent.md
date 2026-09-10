---
name: review-tests
description: Use when reviewing EventHub Playwright tests for correctness, selector reliability, isolation, business-rule coverage, and alignment with the tests folder.
argument-hint: [test file path or blank for all tests]
---

# Review EventHub Tests

Review: $ARGUMENTS

If no path is supplied, review every `tests/*.spec.js` file.

## Review checklist

- Test targets the hosted URL configured in `playwright.config.ts`.
- Login and state setup are self-contained.
- Tests use `data-testid`, roles, labels, placeholders, then stable IDs.
- No XPath, `page.waitForTimeout()`, brittle CSS chains, or unexplained indexes.
- Assertions cover the visible result and relevant URL/state transition.
- Selectors exist in the frontend source and match the current test contract.
- Booking reference, seat, price, event-limit, booking-limit, and access-control assertions match the domain rules.
- Shared sandbox state is handled without parallel execution.

Report findings by severity with file paths and line references. Do not invent issues; distinguish test defects from application defects.
