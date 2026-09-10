---
name: test-strategy
description: Use when assigning EventHub scenarios to Unit, API, Component, or E2E layers. Uses the backend services, frontend components, domain rules, and existing Playwright tests to keep coverage below the E2E layer where possible.
argument-hint: [feature or scenario file]
---

# EventHub Test Strategy

Analyze: $ARGUMENTS

Read `docs/test-scenarios.md`, `.github/agents/eventhub-domain.agent.md`, backend services/controllers, frontend components, and `tests/*.spec.js`.

## Layer decisions

- Pure calculation or reference-generation logic: Unit.
- Express endpoint, validator, authorization, seat, limit, or repository contract: API/integration.
- One React component or conditional state: Component.
- Multi-page booking, login, cancellation, or navigation journey: E2E.

Push assertions down to the lowest layer that adequately proves them, while retaining a small set of critical E2E journeys. Document the source function or endpoint, scenario ID, rationale, risk, and expected runtime for every assignment.

The current Playwright suite is Chromium-only, serial, and targets `https://eventhub.rahulshettyacademy.com`.
