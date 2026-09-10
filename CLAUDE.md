# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview
EventHub is a full-stack event ticket booking platform built for QA training. Users register, browse events, book tickets, manage bookings, and create their own events. Each user operates in an isolated sandbox (their own dynamic events/bookings; 10 seeded "static" events are shared and immutable by everyone).

## Tech Stack
- **Frontend**: Next.js 14 (App Router), React 18, TypeScript, Tailwind CSS, React Query v5, Axios
- **Backend**: Express.js 4, Prisma ORM 5, MySQL 8+, Swagger UI (`/api/docs`)
- **Auth**: JWT (7-day expiry) via `Authorization: Bearer <token>`, bcryptjs password hashing
- **Testing**: Playwright E2E (Chromium only)

## Project Structure
```
eventhub/
├── frontend/            # Next.js 14 app (port 3000)
│   ├── app/              # Pages (App Router): events/, bookings/, admin/, login/, register/
│   ├── components/       # ui/ (primitives), events/, bookings/, auth/, layout/
│   ├── lib/               # api/ (axios clients), hooks/ (React Query), providers.jsx
│   └── types/             # Shared TS interfaces
├── backend/              # Express API (port 3001)
│   └── src/
│       ├── routes/        # Express routers + Swagger JSDoc (auth, events, bookings)
│       ├── controllers/   # Thin HTTP layer, calls services
│       ├── services/      # Business logic, validation, transactions
│       ├── repositories/  # Pure Prisma data access
│       ├── validators/    # express-validator middleware
│       ├── middleware/    # authMiddleware, errorHandler, requestLogger
│       └── config/        # Prisma client singleton, env, swagger
│   └── prisma/schema.prisma, seed.js
├── tests/                # Playwright E2E specs (flat, feature-named)
└── .claude/skills/        # Domain knowledge + slash-command agents (see below)
```

## Architecture Pattern
Backend follows strict layered architecture: **Routes → Controllers → Services → Repositories → Prisma/DB**. Keep business logic (limits, pruning, ref-code generation, seat math) in services, not controllers or repositories.

## Data Model (`backend/prisma/schema.prisma`)
- `User` — email/password, has many `events` and `bookings`
- `Event` — `isStatic` (seeded, shared, immutable) vs user-created (`userId` set); `availableSeats` field only meaningfully tracked for static events (see below)
- `Booking` — belongs to one `Event` + `User`; `bookingRef` is unique; `status` defaults `"confirmed"`

## Commands
```bash
npm run setup         # Install deps in both backend/ and frontend/
npm run dev            # Start frontend + backend concurrently (ports 3000 / 3001)
npm run seed            # Seed 10 static events (backend/prisma/seed.js)
npm run db:push          # Push Prisma schema to DB (non-interactive)
npm run migrate           # prisma migrate dev (interactive, creates migration files)
npm run build              # Build Next.js frontend
npm run lint                 # next lint (frontend only)

npm run test                  # Run all Playwright tests
npm run test:ui                # Playwright UI mode
npm run test:report             # Open last HTML report
npx playwright test tests/<file>.spec.js --reporter=line   # Run a single test file
npx playwright test -g "<test name>" --reporter=line        # Run a single test by title
```
No backend/frontend unit test runner is configured — `npm run test` at the root only runs Playwright E2E.

## Testing Conventions
- Test files: `tests/<feature-name>.spec.js` (flat directory, no subfolders/POM classes currently)
- **Important**: `playwright.config.ts` sets `baseURL` to the hosted sandbox (`https://eventhub.rahulshettyacademy.com`), *not* `localhost:3000`. Existing specs additionally hardcode this same URL in a local `BASE_URL` const rather than relying on the config default — follow that pattern for consistency with existing tests, or check with the user before switching a test to hit a local server.
- `fullyParallel: false` — tests are not designed to run in parallel against the shared sandbox
- Locator priority: `data-testid` > role > label/placeholder > element ID > CSS class. Never use XPath or brittle index-based selectors.
- No `page.waitForTimeout()` — use web-first assertions (`expect(locator).toBeVisible()`, etc.)
- Each test/flow is self-contained: login → action → assert. Auth is done via UI login in a `login(page)` helper (see `tests/booking-management.spec.js`), not via storageState/API shortcuts.
- Test account: `rahulshetty1@gmail.com` / `Magiclife1!`
- Full standard: `.claude/skills/playwright-best-practices/SKILL.md`

## Key Business Rules
- Max **6** user-created events per account — oldest is auto-deleted (FIFO) when the limit is exceeded; static events don't count and can't be edited/deleted
- Max **9** bookings per user — oldest is auto-deleted (FIFO) when exceeded; "Clear All Bookings" removes all at once
- Booking ref format `[FIRST_LETTER]-[6_RANDOM_ALPHANUMERIC]`, where the first letter is the **event title's first letter, uppercased** (e.g. "Tech Summit" → `T-A3B2C1`)
- Seats: static events use the DB `availableSeats` field directly; dynamic (user-created) events compute availability as `totalSeats - sum(that user's booking quantities)`, so the same user can rebook the same event repeatedly for test data
- Refund eligibility is **frontend-only** logic (no backend endpoint): qty = 1 → eligible; qty > 1 → not eligible; shown after a 4s spinner
- Cross-user access to another user's booking/event returns 403 "Access Denied"
- `totalPrice = event.price * quantity`

Full rules/flows/API reference: `.claude/skills/eventhub-domain/` (`business-rules.md`, `user-flows.md`, `api-reference.md`, `ui-selectors.md`).

## Skills & Slash Commands (`.claude/skills/`)
This repo defines a QA-agent workflow as Claude Code skills (mirrored for Codex under `.agents/skills/` and `AGENTS.md` — keep both in sync if you edit one):
- `eventhub-domain`, `playwright-best-practices` — reference knowledge, auto-loaded by the agents below (not user-invocable)
- `/create-scenarios [feature]` — generates functional test scenarios into `docs/test-scenarios.md`
- `/generate-tests [feature]` — writes + self-validates Playwright specs against a real browser
- `/review-tests [file]` — reviews test code against the best-practices standard
- `/test-strategy [scenarios]` — assigns scenarios to test-pyramid layers (Unit/API/Component/E2E)

Each agent's `SKILL.md` lists its required reading order — follow the same order when doing equivalent work manually.

## Code Style
- Backend: JavaScript with JSDoc + Swagger annotations on routes, Express conventions
- Frontend: TypeScript preferred for new files; some existing components are `.jsx` (mixed intentionally, not a migration in progress)
- Tests: JavaScript (`.spec.js`), with step comments (`// Fill booking form`, etc.) and small local helper functions rather than a Page Object Model
