# Drivana

Drivana is a serious ride-hailing MVP for rider and driver workflows. It demonstrates ride requests, proximity-aware driver matching, route estimation, transparent fare calculation, simulated GPS movement, trip lifecycle controls, payment capture simulation, ratings, and trip history in a responsive mobility command center.

> **Product principle:** Move with intent.

## Product surface

The rider view supports pickup and destination selection from named places, route distance and ETA calculation, Go / Comfort / XL ride tiers, fare breakdowns, request matching, driver arrival, trip progress, payment simulation, receipt references, and post-trip ratings. The driver cockpit supports availability status, demand visualization, a next-best request card, and accepting a request into the shared local trip state. The trip history view supports authenticated server history plus session-local completed rides, with loading, error, and empty states.

The map is intentionally a deterministic simulation for this MVP. It uses an SVG route canvas with pickup, destination, and driver markers so the project runs without exposing a third-party map key. GPS movement advances through the trip lifecycle on a timer and is suitable for demos and coursework; real production GPS would require device permissions, a location provider, routing infrastructure, and realtime transport.

## Appropriate stack

This project uses a web-native TypeScript stack, which is the most suitable choice for an interactive browser MVP. TypeScript is JavaScript with static typing, React provides the UI, Express and tRPC provide typed server contracts, Drizzle ORM targets MySQL/TiDB SQL, and Vitest covers deterministic domain rules. HTML is represented by JSX templates and CSS is implemented through the project stylesheet and Tailwind-compatible tokens.

Java, C++, and C# are intentionally not mixed into this browser MVP because they would add service boundaries without improving the rider/driver demo. They remain reasonable choices for future native mobile clients, high-performance routing services, or enterprise services once the product needs those separate deployment targets.

| Layer | Choice | Responsibility |
| --- | --- | --- |
| Client | React 19 + TypeScript | Rider dashboard, driver cockpit, responsive interaction state |
| Styling | CSS tokens + Tailwind 4 utilities | Graphite, orange, blue, mint, glassmorphism, responsive layout |
| Server | Express 4 + tRPC 11 | Typed quotes, ride requests, history, status, payments, ratings |
| Persistence | Drizzle ORM + MySQL/TiDB SQL | Users, drivers, rides, locations, payments, ratings |
| Tests | Vitest | Fare math, routing, matching, lifecycle, payment and rating policies |

## Local development

Use Node.js 20+ and pnpm. Install dependencies, run the development server, and open the local URL shown by the server:

```bash
pnpm install
pnpm dev
```

Run the verification commands before committing:

```bash
pnpm check
pnpm test
pnpm build
```

The repository includes the generated migration under `drizzle/0001_narrow_wolverine.sql`. Apply schema changes through the project’s migration workflow; do not commit real credentials or a populated `.env` file. Use the managed environment configuration for runtime values; no credential file is needed in the repository.

## Database model

The SQL model includes `users`, `drivers`, `rides`, `ride_locations`, `payments`, and `ratings`. Protected server procedures scope ride history and write operations to the authenticated rider, validate ride status transitions, require completed trips before payment or ratings, and verify that a rating references the assigned driver when one exists.

## Security boundary

The MVP intentionally contains no exposed API keys and no AI integrations. External map APIs are not required. Server secrets are supplied through the runtime environment and are never hardcoded into client code. The payment experience is a clearly labeled simulation and does not process real money or collect card data.

## Project layout

```text
client/src/pages/Home.tsx     Drivana rider, driver, map, history UI
client/src/index.css          Visual system and responsive layout
drizzle/schema.ts             SQL table definitions and inferred types
server/db.ts                  Database query helpers
server/routers.ts              Typed protected/public procedures
shared/ride.ts                 Route, fare, matching, lifecycle policies
shared/ride.test.ts            Domain unit tests
server/*.test.ts               Server and configuration tests
```

## MVP extension path

A production deployment would add real device GPS permissions, websocket or server-sent event updates, a geocoding/routing provider, driver identity verification, rate limiting, fraud controls, real payment provider webhooks, push notifications, and a native driver app. Those are intentionally outside this secure simulation MVP.
