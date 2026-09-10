---
name: eventhub-domain
description: Use when creating or reviewing EventHub tests, scenarios, API checks, or user-flow documentation. Provides the EventHub data model, business rules, hosted environment, and selectors used by the tests folder.
---

# EventHub Domain Agent

Use this repository as the source of truth. Read `backend/prisma/schema.prisma`, `backend/src/services/`, `frontend/app/`, `frontend/components/`, and `tests/*.spec.js` before making domain claims.

## Runtime and test environment

- Hosted UI and API environment used by the current E2E tests: `https://eventhub.rahulshettyacademy.com`
- Playwright test directory: `tests/`
- Browser: Chromium
- Tests run serially with `fullyParallel: false`
- Disposable seeded test account: `rahulshetty1@gmail.com` / `Magiclife1!`

## Core business rules

- Static seeded events are shared and immutable.
- Each user may have up to 6 user-created events; overflow removes the oldest event.
- Each user may have up to 9 bookings; overflow removes the oldest booking.
- Booking references use `[EVENT_TITLE_FIRST_LETTER_UPPERCASE]-[6_ALPHANUMERIC]`.
- Static-event seats use stored availability; dynamic-event availability is calculated per user.
- Booking price is `event.price * quantity`.
- Cancelling a booking removes it and restores relevant availability.
- A single-ticket booking is refund eligible; multi-ticket bookings are not. The UI displays the result after its spinner delay.
- Cross-user booking access must return `403` with an access-denied response.

## Current UI contract from `tests/booking-management.spec.js`

- Login email: `getByPlaceholder('you@email.com')`
- Login password: `getByLabel('Password')`
- Login button: `#login-btn`
- Event card: `getByTestId('event-card')`
- Book button: `getByTestId('book-now-btn')`
- Full name: `getByLabel('Full Name')`
- Customer email: `#customer-email`
- Customer phone: `getByPlaceholder('+91 98765 43210')`
- Confirm booking: `.confirm-booking-btn`
- Booking reference: `.booking-ref`
- Booking card: `getByTestId('booking-card')`
- View details: role link `View Details`
- Clear bookings: role button matching `/clear all bookings/i`
- Empty state: `No bookings yet`
- Refund check: `#check-refund-btn`
- Cancel confirmation: `#confirm-dialog-yes`

Prefer `data-testid`, semantic roles, labels, and placeholders in that order. Do not invent selectors; verify them in the frontend or tests.
