# StudySwap roadmap

StudySwap is a student-to-student marketplace for second-hand academic and competitive-exam books, with
AI-recommended pricing and a Premium Book Finder. The work is split into small, shippable milestones.

## Platform decision

The app runs as a single TanStack Start (React + Vite + TypeScript + Tailwind) project on Netlify:
server functions / API routes replace a separate Express server, **Netlify Database** (managed Postgres +
Drizzle) replaces Supabase Postgres, **Netlify Identity** replaces Supabase Auth, **Netlify Blobs** stores
book photos, and **Netlify AI Gateway** provides the LLM without managing keys. One repo, one deploy.
Razorpay remains the payment gateway. If the owner prefers Supabase/Vercel, swap in milestones 2–4.

## Milestones

1. **Product surface (done)** — branded home page (search, categories, featured/recent, AI pricing
   explainer, Premium Finder promo, savings calculator, how it works), Browse Books with search/filter/sort,
   book detail with estimated savings and report/contact flows, Sell a Book with the AI price review and
   explicit seller consent (no manual price field), Find a Book with Free/Premium tiers and fee/refund
   disclosure plus live matching, dashboard preview (requests, offer comparison, listings, notifications),
   login screen and policy pages. All data comes from `src/data/fixtures.ts` (sample data).
2. **Data model** — Drizzle schema in `db/schema.ts`: profiles, books, book_images, price_assessments,
   book_requests, seller_offers, payments (unique gateway order/payment IDs for idempotency), refunds,
   notifications, reports, audit_events, settings (premium fee, matching days). Status check constraints,
   indexes on category/status/isbn/normalised title. Seed script with clearly labelled demo rows.
3. **Accounts** — Netlify Identity sign-up/login/logout, email verification, profile (display name, city,
   preferred contact method, request-notification opt-in), `admin` role. Server-side auth guard helper used
   by every protected server function; ownership checks on listings, requests and offers.
4. **Real marketplace** — server functions for create/edit/deactivate/delete listings, browse/search/filter
   queries replacing fixtures, photo upload to Blobs (type/size validation, served via Image CDN),
   duplicate-submission protection, listing reports, rate limiting.
5. **AI pricing service** — server-only: normalise input → query real comparables from the DB → LLM via AI
   Gateway with structured output (price, range, confidence, factors, cited comparable IDs only) →
   deterministic safeguards (positive INR, ≤ original, discount bounds; otherwise "Awaiting Price Review")
   → store assessment + model version. `src/lib/pricing.ts#previewPrice` becomes the labelled fallback.
   Re-assessment required when pricing inputs change; pricing history per listing.
6. **Book Finder & matching** — persist requests with the status machine (Draft → Awaiting Payment →
   Searching → Seller Responses Received → Offer Selected → Completed / No Match / Cancelled / Refund
   Pending / Refunded), move `src/lib/matching.ts` server-side, seller notifications to opted-in sellers
   (deduplicated), seller offers validated through AI pricing, offer accept/decline/withdraw, consent-based
   contact sharing / in-app messages.
7. **Razorpay payments** — server-created orders, signature verification + webhook handler with idempotency,
   Premium activated only after server verification, failed/pending/refunded handling, clearly labelled
   mock mode for local dev when keys are missing. `.env.example` with placeholders.
8. **Notifications & deadlines** — in-app notifications table + bell, scheduled function for deadline
   reminders and auto "No Match" + refund eligibility; optional email when a provider is configured
   (delivery reported only when confirmed).
9. **Admin dashboard** — role-protected: users, reports, requests & outcomes, verified payments, refund
   review, fee/matching-period settings, AI pricing failures/low confidence, basic analytics.
10. **Tests & hardening** — unit tests for savings maths, matching, payment signature verification and
    authorization; workflows A–G from the brief; integrations checklist (configured vs. not) in README.
