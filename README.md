# Fair Flash-Sale / Ticketing Platform

## Problem

Limited-inventory flash sales can receive huge numbers of simultaneous requests, creating risks of:

- overselling
- duplicate checkout
- inventory corruption
- server overload

## Solution

User
↓
Waiting Room
↓
Queue Admission
↓
Ticket Hold
↓
Checkout
↓
Confirmation

## Current Features

- Virtual waiting room
- Queue admission
- Atomic ticket reservation
- Time-limited ticket holds
- Automatic expired-hold release
- Idempotent checkout
- MongoDB persistence
- JWT authentication
- bcrypt password hashing
- Admin login
- Booking history
- k6 load testing

## Concurrency Protection

Ticket reservation uses an atomic database update with an availability condition. The reservation succeeds only when enough tickets are available in the database at the exact time of the update. This prevents concurrent requests from reserving more tickets than the live inventory allows.

## Idempotent Checkout

Checkout uses an Idempotency-Key to prevent duplicate orders when the same checkout request is repeated. If a user retries the same confirmation or a network issue causes a duplicate submission, the system treats it as the same logical transaction instead of creating multiple orders.

## Load Test Evidence

A local k6 stress test was run using 200 concurrent virtual users over 10 seconds against an initial inventory of 100 tickets. The test recorded:

- Successful holds: 100
- Controlled sold-out responses: 926
- Custom checks: 1026/1026 passed
- Final available inventory: 0

This was a local concurrency validation exercise for the demo environment. It validates no-oversell behavior and queue/hold protection under load. HTTP 409 responses are intentional sold-out responses after inventory was exhausted.

## Security

### Currently implemented

- JWT authentication
- bcrypt password hashing
- role-based admin login
- idempotency key
- atomic inventory update
- waiting room

### Production improvements

The following are recommended production deployment improvements and are not claimed as already implemented:

- Redis rate limiting
- CAPTCHA
- WAF
- HTTPS
- secure token/cookie strategy
- monitoring
- centralized logging
- secrets manager

## Rate Limiting / Bot Protection

This demo does not add new middleware or change active business logic. Production deployment should add:

- IP rate limiting
- user/account rate limiting
- CAPTCHA
- bot detection
- WAF
- Redis-based distributed rate limiting

These are future production safeguards and should be implemented as part of a hardened deployment layer.

## Local Setup

1. Copy `.env.example` to a local environment file such as `.env.local`.
2. Configure the backend MongoDB connection and JWT secret values locally.
3. Start the backend API.
4. Start the frontend app.
5. Use the app as a demo. Do not commit environment files.

## Notes

- Passwords are never returned to the frontend from the backend.
- JWT secret values and MongoDB credentials are kept in local environment files, not committed to source control.
- The project keeps frontend access limited to the public UI and does not expose server-side secrets to the browser.
- The `.gitignore` file is set to exclude environment files and build artifacts.

## Project Status

This project is a working frontend + backend demo focused on fair flash-sale protection, queue admission, ticket holds, and idempotent checkout. It is intended for local demo and hackathon presentation use, with production hardening added as a separate deployment step.
