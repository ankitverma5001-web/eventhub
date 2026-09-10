---
name: create-scenarios
description: Use when creating EventHub functional test scenarios from the domain, frontend flows, backend services, and current tests. Covers happy paths, rules, security, errors, boundaries, and UI states.
argument-hint: [feature or blank for full suite]
---

# Create EventHub Scenarios

Create scenarios for: $ARGUMENTS

Read `.github/agents/eventhub-domain.agent.md`, `docs/test-scenarios.md`, the relevant frontend pages/components, backend services, and existing tests before writing.

For each scenario include:

- ID and title
- Category: Happy Path, Business Rule, Security, Negative, Edge Case, or UI State
- Priority
- Preconditions
- Numbered steps
- Expected results
- Business rule or source behavior
- Suggested layer: Unit, API, Component, or E2E

Use the repository numbering convention: TC-001-099 happy path, TC-100-199 business rules, TC-200-299 security, TC-300-399 negative, TC-400-499 edge cases, TC-500-599 UI state.

Cover EventHub login, event browsing, booking, booking references, price and seats, limits, cancellation, clear-all, refund eligibility, static-event immutability, and cross-user access as applicable. Keep selectors and URLs consistent with the current tests.
