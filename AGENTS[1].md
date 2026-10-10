# AGENTS.md

StudySwap — a second-hand academic book marketplace with AI-recommended prices and a Premium Book Finder.
**Continue from [PLAN.md](./PLAN.md).** Milestone 1 (the UI surface) is done. The next step is milestone 2 (data model).

## Stack
TanStack Start (React 19, TanStack Router file routes, Vite 7), Tailwind CSS 4, TypeScript strict,
lucide-react. Deployed on Netlify. Use Netlify primitives for persistence (Database + Drizzle, Identity,
Blobs, AI Gateway). Read the skills in `.agents/skills` and `/opt/buildhome/.claude/skills` first.

## Layout
```
src/
  data/fixtures.ts     # ALL sample data + domain types + finderSettings. Swap for DB queries later.
  lib/pricing.ts       # inr(), calculateSavings(), previewPrice() — rule-based preview/fallback pricing
  lib/matching.ts      # request ↔ listing scoring (ISBN first, title/author/edition/condition/budget/city)
  components/          # SiteHeader, SiteFooter, Logo, BookCover (typographic covers), BookCard, Badges
  routes/
    index.tsx          # home
    books/index.tsx    # browse (search params validated in validateSearch)
    books/$bookId.tsx  # detail
    sell.tsx           # 3-step listing flow; no manual price input by design
    find.tsx           # Book Finder request form + matching results
    dashboard.tsx      # buyer/seller dashboard preview
    login.tsx, legal/$page.tsx
  styles.css           # Tailwind @theme tokens (paper/ink/forest/marigold/brick) + .btn-*, .card, .field, .chip
```

## Conventions & decisions
- Design: warm "paper" palette, Fraunces display + Instrument Sans body (Google Fonts in `__root.tsx`).
  Use the theme tokens and component classes in `styles.css`; don't introduce new colours ad hoc.
- Sellers never enter a price. The AI price is required, and the seller must explicitly consent to it.
  Changing a pricing input means the assessment is stale and a new one is required.
- Never fabricate market data. Low or no comparables means the result is labelled "Estimated · low confidence".
- Savings from seller-reported original prices are always labelled estimates.
- Premium benefits must only activate after server-side payment verification, never from client state.
- Sample data must stay visibly labelled (`<SampleTag />`).
- Import alias `@/` → `src/`. `noUnusedLocals` is on.
