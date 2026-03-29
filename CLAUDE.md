# Lorne Email-to-Booking SaaS

## What This Is
A micro-SaaS that reads inbound restaurant booking emails, classifies them, extracts booking details, and drafts responses in the restaurant owner's voice. Built around Katie Exton's Lorne restaurant (76 Wilton Road, Victoria, London) as the founding use case. The human is always in the loop - nothing sends without approval.

**Full product plan:** `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/iCloud/4. Notes/Lorne Email-to-Booking SaaS - Product Plan.md`

## First Milestone
Gmail connection + thread ingestion. Can we read Katie's inbox, display overnight emails in a dashboard, and classify them by type (booking / modification / dietary / supplier / other)? No AI drafting yet - just prove the inbox view is useful.

## Tech Stack
| Layer | Choice | Why |
|---|---|---|
| Framework | Next.js 14+ (App Router) | React ecosystem, SSR for dashboard, API routes |
| DB | Supabase Postgres | Hosted Postgres, RLS, generous free tier. Skip pgvector for now |
| Auth | Google OAuth via NextAuth | One flow grants Gmail API access too |
| Jobs | Inngest | Serverless job scheduling - poll Gmail on schedule + handle webhooks |
| LLM | Claude Sonnet (via Anthropic API) | Fast, cheap, good enough for v1. Upgrade specific steps later if needed |
| Style retrieval | Simple tag matching | Tag emails by type, retrieve 3 matching examples. No vector DB |
| Hosting | Vercel | Zero-config Next.js deployment |
| Monitoring | Sentry | PostHog is premature with 1 user |

Total moving parts: Next.js + Supabase + Inngest + Gmail API + Claude API. Five services.

## Key Files
- `src/app/dashboard/` - Email queue and review UI
- `src/app/settings/` - Restaurant config, style training
- `src/app/auth/` - Login/callback
- `src/app/api/` - API routes
- `src/lib/gmail/` - Gmail API client
- `src/lib/llm/` - Claude API wrapper, prompts
- `src/lib/style/` - Style profile generation and matching
- `src/lib/booking/` - Booking extraction
- `src/lib/db/` - Supabase client, queries
- `supabase/migrations/` - Database schema

## Build Order
1. Gmail connection + thread ingestion (CURRENT)
2. Manual review inbox - dashboard showing overnight emails, labelled by type
3. Structured extraction - LLM classification and detail extraction
4. Style profile + draft generation - upload Katie's examples, generate profile
5. One-click send - approve draft, send from Katie's Gmail
6. Edit feedback capture - store diffs for later analysis
7. Booking handoff card - structured card with details, deep link to OpenTable

## Deployment
- **Hosting:** Vercel (frontend + API) + Supabase (DB + auth)
- **Domain:** TBD - pick a name and register
- **Env vars needed:**
  - `ANTHROPIC_API_KEY` - available in `~/.claude/.env`
  - `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` - need to create in Google Cloud Console
  - `SUPABASE_URL` / `SUPABASE_ANON_KEY` / `SUPABASE_SERVICE_ROLE_KEY` - from Supabase project
  - `INNGEST_EVENT_KEY` / `INNGEST_SIGNING_KEY` - from Inngest dashboard
  - `NEXTAUTH_SECRET` - generate with `openssl rand -base64 32`
  - `SENTRY_DSN` - from Sentry project

## Conventions
- British English in all user-facing copy
- Supabase migrations for all schema changes
- Claude Sonnet for all LLM calls in v1

## Insights
