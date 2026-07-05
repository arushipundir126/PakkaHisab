# PakkaHisab

The paper khata, reimagined. A complete business operating system for Indian SME owners.

**Live:** [pakka-hisab.vercel.app](https://pakka-hisab.vercel.app)

## Overview

PakkaHisab is designed to feel like a smart assistant, not accounting software. Scan a bill, speak a sale, or tap a product, and the app handles inventory, customer credit, profit, and tax on its own. Every feature is held to one bar: it must feel faster than doing it on paper. If it feels like a task, the design is wrong.

## Problem Statement

Most Indian kirana stores, medical shops, hardware stores, and small manufacturers still run on a handwritten khata. That means:

- No real picture of profit once rent, salaries, and other fixed costs are accounted for.
- Stock levels tracked from memory, so stockouts and over-ordering both happen often.
- Customer credit (udhaar) scattered across notebooks with no easy way to follow up.
- GST and tax estimates done manually or left to a CA once a year.
- Existing accounting software is built for accountants, not shopkeepers, so it goes unused.

## Solution

PakkaHisab meets the merchant where they already are: a bill in hand, a sale to log, a question about the business. It reads bills with AI instead of asking for manual data entry, keeps inventory in sync automatically, and answers business questions in plain language instead of requiring reports to be interpreted.

## Features

- **Bill scanning** - Photograph a printed receipt, GST invoice, delivery challan, handwritten bill, or a page from a khata. AI extracts the line items, matches them to your catalog, flags anything new, warns about duplicates, and learns from your corrections over time.

- **Voice-first entry** - Speak items in Hinglish ("2 doodh, 3 Parle-G") and watch them land in a live cart. Commands like "agla" (save and next), "khatam" (finish), and "balance batao" (read the total aloud) keep your hands free. Supports udhaar (credit) by voice.

- **Quick manual entry** - Tap +/- on products to log a sale in seconds. Frequently sold items float to the top automatically. Sale is the default; purchase and expense tuck behind a toggle.

- **Product catalog** - Per-product purchase and selling price, GST rate, units, and variants (sizes/packs), with fuzzy search by name, brand, item number, or category.

- **Inventory management** - Real-time stock with color-coded alerts (green / amber / red). Consumption-rate tracking predicts days until stockout. AI suggests what to order, what to slow down on, and what is about to expire.

- **Customer credit (udhaar)** - Track who owes what, with a per-customer ledger and full transaction history. One-tap WhatsApp reminders.

- **Reports and profit** - Daily / weekly / monthly / yearly views. Cash flow, sales vs purchases, top products, and expense breakdowns. True net profit factors in fixed costs. GST liability calculated automatically.

- **AI business advisor** - Ask anything in plain language: "Why is cash low?", "Which customer owes the most?", "Is this month better than last?" Gets a data-backed answer in seconds, in your preferred language.

- **Reconciliation** - Match recorded transactions against reality and resolve gaps.

- **Transaction history** - Every sale, purchase, and expense on the dashboard. Tap to expand, void mistakes with automatic stock restoration.

## How It Works

1. **Capture** - A bill is scanned, a sale is spoken, or a product is tapped.
2. **Understand** - AI extracts or parses the input and matches it against the store's own product catalog.
3. **Confirm** - The merchant reviews a short confirm screen; low-confidence fields are flagged for a second look.
4. **Update** - Inventory, customer balances, and cash position update automatically on save.
5. **Learn** - Any correction the merchant makes is remembered and used to improve future extractions for that store.
6. **Advise** - Reports, alerts, and the AI advisor turn that data into decisions: what to order, who owes money, whether this month is better than last.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Next.js 14 (App Router, TypeScript strict) |
| Styling | Tailwind CSS + shadcn/ui |
| Database | Supabase (PostgreSQL + Row Level Security) |
| ORM | Prisma |
| Auth | Supabase Auth (email OTP, magic link) |
| AI | Google Gemini (bill extraction, voice parsing, advisor) |
| Voice | Web Speech API + voice activity detection |
| Charts | Recharts |
| Deployment | Vercel |

## System Architecture

```
Client (Next.js App Router, React)
   |
   |  scan / voice / quick entry / chat
   v
API routes (Next.js Route Handlers)
   |
   |-- Supabase Auth        session + identity
   |-- Supabase Postgres    tenant data, scoped by store, RLS on every table
   |-- Supabase Storage     uploaded bill images, private access
   |-- Google Gemini        vision extraction, voice parsing, advisor chat
   v
Merchant-facing screens (dashboard, inventory, reports, advisor)
```

Each store is a tenant. Every table carries a `store_id`, and Row Level Security ensures a merchant can only ever see their own data.

## Installation & Setup

Prerequisites: Node.js 20+, npm, and a Supabase project.

```bash
# 1. Install dependencies (also runs prisma generate)
npm install

# 2. Set up environment variables
cp .env.local.example .env.local
# Fill in the values described in that file

# 3. Push the database schema
npx prisma migrate dev

# 4. (Optional) Seed a demo store
npm run seed:demo

# 5. Start the dev server
npm run dev
```

## Project Structure

```
app/
  (auth)/          Login, signup, onboarding
  (dashboard)/     Home, scan, entry, voice, inventory, products,
                   customers, ledger, reports, advisor, reconcile,
                   transactions, settings
  api/             Route handlers (scan, entry, ai, inventory,
                   reports, import, voice, cron, ...)
components/        React components organized by feature
lib/               Business logic, AI prompts, parsers, utilities
hooks/             Custom React hooks
types/             TypeScript type definitions
prisma/            Schema and migrations
data/seeds/        Product catalog templates by store type
scripts/           Seed and maintenance scripts
```

## API & Database

The API is organized by domain: `scan`, `entry`, `products`, `inventory`, `customers`, `transactions`, `reports`, `ai`, `import`, and `reconciliation`, each scoped to the authenticated merchant's store.

Core data model: stores, products (with variants), inventory, stock movements, customers, transactions and transaction items, document uploads, extraction corrections, fixed costs, AI conversations, and periodic reports. A read-only seed table of curated product data powers store templates and catalog suggestions.

## AI Features

- **Bill extraction** - Vision-based parsing of printed and handwritten bills into structured line items, with confidence scoring per field.
- **Catalog matching** - Extracted items are matched to the merchant's own catalog, exact match first, then fuzzy.
- **Correction learning** - Every manual correction is stored per store and fed back into future extractions as few-shot context, so accuracy improves over time without retraining anything.
- **Voice parsing** - Spoken Hinglish sales are parsed into structured cart items in real time.
- **Ordering intelligence** - Consumption-rate and expiry logic generate daily "order now / slow down / watch expiry" suggestions.
- **Conversational advisor** - A chat interface answers business questions using the merchant's own transaction, inventory, and cost data.

## Security & Privacy

- Row Level Security enforced on every tenant data table, scoped to the store.
- Every API route checks the session before touching any data.
- Server-only secrets are never exposed to the client.
- File uploads are validated server-side and kept in access-controlled storage.
- Sensitive actions (deletes, voids) require explicit confirmation before they take effect.

## Roadmap / Future Scope

- Deeper regional-language voice support beyond the current Hinglish parsing.
- Offline-first support for low-connectivity areas.
- WhatsApp Business API integration for reminders and reports, beyond the current one-tap flow.
- Richer, exportable CA-ready tax statements.
- Native mobile app.

## Challenges & Learnings

- **Casing mismatches at the data boundary** - Postgres/PostgREST returns `snake_case` while the app's types are `camelCase`. Silent mismatches showed up as `NaN` in the UI. The fix was to normalize every response to camelCase at the fetch boundary rather than assuming field names line up.
- **Designing for the 90% case** - Sales are the overwhelming majority of daily entries, so quick entry defaults to Sale and tucks Purchase/Expense behind a secondary toggle instead of presenting all three equally.
- **Teaching the AI without training a model** - Rather than fine-tuning, every merchant correction to an AI extraction is stored and replayed as a few-shot example on the next scan for that store, so accuracy compounds per-merchant over time.

## License

MIT - see [LICENSE](LICENSE).

## Author

Built by [Arushi Pundir](https://github.com/arushipundir126).
