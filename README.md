# Campus Equipment Booking API

A serverless REST API for booking campus equipment, built with Cloudflare Workers and Cloudflare D1.

## Base URL

https://campus-booking.jutatip-sri.workers.dev/api

## Tech Stack

- Cloudflare Workers
- Cloudflare D1 (SQLite)
- TypeScript
- Hono
- Wrangler CLI

## Project Structure

campus-booking/
  src/index.ts              Worker entry point and route handlers
  schema.sql                D1 database schema
  wrangler.jsonc            Wrangler configuration
  package.json
  tsconfig.json
  README.md
  API_CONTRACT.md
  AI_LOG.md
  QUALITY_GATE_REVIEW.md

## Setup

Install dependencies:

npm install

## Database Setup

Apply the schema:

npx wrangler d1 execute campus-booking --file=./schema.sql

## Run Locally

npm run dev

Worker will be available at http://localhost:8787

## Deploy

npm run deploy

## Generate Types

npm run cf-typegen

## Endpoints

| Method | Endpoint | Description | Success | Errors |
|---|---|---|---|---|
| GET | /equipment | List all equipment | 200 | - |
| GET | /bookings | List all bookings | 200 | - |
| GET | /bookings/:id | Get one booking | 200 | 404 |
| POST | /bookings | Create a booking | 201 | 400, 404, 409 |
| PATCH | /bookings/:id | Update a booking | 200 | 400, 404, 409 |
| DELETE | /bookings/:id | Delete a booking | 204 | 404 |

## Schema

Equipment
| Field | Type |
|---|---|
| id | TEXT (PK) |
| name | TEXT |
| location | TEXT |

Booking
| Field | Type |
|---|---|
| id | TEXT (PK) |
| equipmentId | TEXT (FK to Equipment.id) |
| borrowerName | TEXT |
| startAt | TEXT (ISO 8601) |
| endAt | TEXT (ISO 8601) |
| purpose | TEXT |

Relationship: Equipment 1 -- N Booking

## Example Requests

Create a booking:

curl -i -X POST https://campus-booking.jutatip-sri.workers.dev/api/bookings -H "Content-Type: application/json" -d '{"equipmentId":"eq-1","borrowerName":"Somchai Jaidee","startAt":"2026-10-20T09:00:00.000Z","endAt":"2026-10-20T11:00:00.000Z","purpose":"Class presentation"}'

Get all bookings:

curl -i https://campus-booking.jutatip-sri.workers.dev/api/bookings

Get one booking:

curl -i https://campus-booking.jutatip-sri.workers.dev/api/bookings/BOOKING_ID

Update a booking:

curl -i -X PATCH https://campus-booking.jutatip-sri.workers.dev/api/bookings/BOOKING_ID -H "Content-Type: application/json" -d '{"startAt":"2026-10-20T10:00:00.000Z","endAt":"2026-10-20T12:00:00.000Z"}'

Delete a booking:

curl -i -X DELETE https://campus-booking.jutatip-sri.workers.dev/api/bookings/BOOKING_ID

Get all equipment:

curl -i https://campus-booking.jutatip-sri.workers.dev/api/equipment

## Validation Rules

- equipmentId must refer to an existing equipment record.
- startAt and endAt must be valid ISO 8601 dates.
- startAt must be earlier than endAt.
- Two bookings for the same equipment cannot overlap.
- Back-to-back bookings are allowed.
- On update, the current booking is excluded from the overlap check.

## Overlap Rule

newStart < existingEnd AND newEnd > existingStart

## Error Format

All errors return JSON:

{ "error": "Error message" }

## HTTP Status Codes

| Code | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 404 | Not Found |
| 409 | Conflict |

## Testing

| # | Test | Status |
|---|---|---|
| 1 | GET /equipment | 200 PASS |
| 2 | GET /bookings | 200 PASS |
| 3 | POST /bookings | 201 PASS |
| 4 | GET /bookings/:id | 200 PASS |
| 5 | PATCH /bookings/:id | 200 PASS |
| 6 | Invalid time | 400 PASS |
| 7 | Non-existent equipment | 404 PASS |
| 8 | Overlapping booking | 409 PASS |
| 9 | DELETE /bookings/:id | 204 PASS |

## Documentation

- API_CONTRACT.md - full API contract
- AI_LOG.md - AI assistance log
- QUALITY_GATE_REVIEW.md - quality gate review
