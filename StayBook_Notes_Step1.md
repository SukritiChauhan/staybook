# StayBook: Step 1 Notes (Requirements and Scope)

## What we're building
A hotel booking backend (APIs only, no frontend). Tested with Postman or curl.

## Core features
- Search hotels by city, dates, price
- Check room availability for a date range
- Book a room
- Cancel a booking
- Never double-book a room

## Quality goals
- Search is fast
- Booking is correct, even under heavy traffic
- Retrying a request is safe

## Out of scope (for now)
- Frontend
- Real payments (mock later)
- Complex login

## Entities (rough)
User, Hotel, Room, Booking (check-in, check-out dates)

---

## Concept 1: Race condition
**Problem:** Two users book the last room at the same moment. Code does "check if free, then save". Both checks pass before either save happens, so both bookings get saved.

**Why it happens:** Check and save are two separate steps. Another request can slip in between them.

**Fixes (we will pick one in a later step):**
- Database transaction with a row lock (pessimistic locking)
- Version column, retry on conflict (optimistic locking)
- Database unique constraint that makes a duplicate booking impossible
- Distributed lock (Redis), usually overkill here

**Interview line:** "Check-then-act is not atomic, so I need the database to enforce the rule, not just my application code."

## Concept 2: Idempotency
**Meaning:** Doing the same request many times gives the same result as doing it once.

**Problem:** User clicks "Book", the network drops, they don't see a response, they click again. Without protection we create two bookings (and with real payments, charge twice).

**Fix:** The client sends an Idempotency-Key (a unique ID) with each booking request. The server stores it. If the same key arrives again, the server returns the original result instead of creating a new booking.

**Correct outcome for the user story:** exactly 1 booking.

**My mistake in the test:** I assumed this was about money going missing. The real issue is duplicate bookings from retries. (Payment mismatch is a related problem, solved later with mock payments and statuses like PENDING, CONFIRMED, FAILED.)

## Concept 3: Read-heavy vs write-heavy paths
**Observation:** Search is used far more than booking (read-heavy). Booking is rare but must be perfectly correct (write-critical).

**So we treat them differently:**
- Search: can use caching (Redis), indexes, and slightly stale data is acceptable.
- Booking: goes straight to the database with strict correctness. No stale data allowed.

**Trade-off:** Caching makes search fast but cached availability can be out of date, so the booking step must always re-check the real database.

## Why we skip payments and frontend first
Not because they are too hard. The learning and the interview value are in the backend problems (concurrency, idempotency, caching). Payments and UI add work without teaching those. Good engineering means starting with the riskiest, most valuable part first.

---

## Self-check questions (revise these in a week)
1. What is a race condition, and why does check-then-save cause one?
2. What is idempotency, and how does an Idempotency-Key work?
3. Why can search use a cache while booking cannot?
4. Name two ways to prevent double-booking.

## Next: Step 2
Choosing the tech stack (language, framework, database, cache) and justifying each choice.
