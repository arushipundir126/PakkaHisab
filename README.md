# PakkaHisab

A complete business operating system for Indian SME owners. Scan bills, manage inventory, track customers, understand profit, pay taxes correctly, and get AI-powered suggestions - all in one place. Designed to feel like a smart assistant, not accounting software.

**Live:** [pakka-hisab.vercel.app](https://pakka-hisab.vercel.app)

> Core principle: every feature must feel faster than doing it on paper. If it feels like a task, the design is wrong.

## What it does

PakkaHisab replaces the paper khata (ledger) that millions of Indian shopkeepers still use. It turns the daily grind of tracking a small business into a few taps or a few spoken words.

### Core features

- **Bill scanning** - Photograph a printed receipt, GST invoice, delivery challan, handwritten bill, or a page from a khata. AI extracts the line items, matches them to your catalog, flags anything new, warns about duplicates, and learns from your corrections over time.

- **Voice-first entry** - Speak items in Hinglish ("2 doodh, 3 Parle-G") and watch them land in a live cart. Commands like "agla" (save and next), "khatam" (finish), and "balance batao" (read the total aloud) keep your hands free. Supports udhaar (credit) by voice.

- **Quick manual entry** - Tap +/- on products to log a sale in seconds. Frequently sold items float to the top automatically. Sale is the default; purchase and expense tuck behind a toggle.

- **Product catalog** - Per-product purchase and selling price, GST rate, units, variants (sizes/packs), and fuzzy search by name, brand, item number, or category.

- **Inventory management** - Real-time stock with color-coded alerts (green / amber / red). Consumption-rate tracking predicts days until stockout. AI suggests what to order, what to slow down on, and what is about to expire.

- **Customer credit (udhaar)** - Track who owes what, with a per-customer ledger and full transaction history. One-tap WhatsApp reminders.

- **Reports and profit** - Daily / weekly / monthly / yearly views. Cash flow, sales vs purchases, top products, and expense breakdowns. True net profit factors in fixed costs (rent, salaries, electricity). GST liability calculated automatically.

- **AI business advisor** - Ask anything in plain language: "Why is cash low?", "Which customer owes the most?", "Is this month better than last?" Gets a data-backed answer in seconds, in your preferred language.

- **Reconciliation** - Match recorded transactions against reality and resolve gaps.

- **Transaction history** - Every sale, purchase, and expense on the dashboard. Tap to expand, void mistakes with automatic stock restoration.

## Tech stack

| Layer | Technology |
|-------|-----------|
| Framework | Next.js 14 (App Router, TypeScript strict) |
| Styling | Tailwind CSS + shadcn/ui |
| Database | Supabase (PostgreSQL + Row Level Security) |
| ORM | Prisma |
| Auth | Supabase Auth (email OTP, magic link) |
| File storage | Supabase Storage (private, RLS enforced) |
| AI | Google Gemini (bill extraction, voice parsing, advisor) |
| Voice | Web Speech API + voice activity detection |
| Charts | Recharts |
| Rate limiting | Upstash Redis (optional) |
| Deployment | Vercel |

## Getting started

Prerequisites: Node.js 20+, npm, and a Supabase project.

```bash
# 1. Install dependencies (also runs prisma generate)
npm install

# 2. Set up environment variables
cp .env.local.example .env.local
# Fill in your Supabase, Gemini, and app keys

# 3. Push the database schema
npx prisma migrate dev

# 4. (Optional) Seed a demo store
npm run seed:demo

# 5. Start the dev server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

### Environment variables

See [.env.local.example](.env.local.example) for the full annotated list. The essentials:

| Variable | Purpose |
|----------|---------|
| `NEXT_PUBLIC_SUPABASE_URL` / `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase client |
| `SUPABASE_SERVICE_ROLE_KEY` | Server-only Supabase admin key |
| `GOOGLE_AI_API_KEY` | Gemini (extraction, voice, advisor), server only |
| `DATABASE_URL` / `DIRECT_URL` | Prisma runtime pool + migration connection |
| `NEXT_PUBLIC_APP_URL` | App base URL |
| `UPSTASH_REDIS_REST_URL` / `UPSTASH_REDIS_REST_TOKEN` | Rate limiting (optional) |
| `CRON_SECRET` | Protects scheduled cron endpoints |
| `NEXT_PUBLIC_UMAMI_WEBSITE_ID` | Analytics (optional) |

Keys marked server-only must never be prefixed with `NEXT_PUBLIC_`.

## Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start the dev server |
| `npm run build` | Production build |
| `npm run start` | Serve the production build |
| `npm run lint` | ESLint |
| `npm test` | Run tests (Vitest) |
| `npm run test:watch` | Tests in watch mode |
| `npm run seed:demo` | Seed a demo store with sample data |

## Project structure

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

## Security

- Row Level Security on every table, scoped by `store_id`.
- No secrets in `NEXT_PUBLIC_` variables; server keys stay server-side.
- Every API route checks the session and scopes queries to the store.
- Private storage buckets with RLS on all file access.
- Server-side MIME and size validation on uploads.
- Rate limiting on sensitive endpoints via Upstash.

## Target users

Kirana stores, medical shops, hardware stores, distributors, small manufacturers - anyone in India who tracks daily business manually.

## License

Private - All rights reserved.
