# ShopSense 3.0 — AI Business Analytics Platform

Turn any business file into dashboards, statistical analysis, customer segments, and plain-English AI insights — with an editorial serif UI (Steep design system), business-grade auth, and dataset persistence.

ShopSense 3.0 is a full-stack Next.js application. Upload a CSV, TSV, XLSX, or JSON file and the platform parses it, profiles every column, grades data quality, renders KPIs and charts, detects correlations and anomalies, runs K-Means segmentation with automatic cluster count selection, and generates AI-authored executive summaries, findings, and personas you can ask follow-up questions about.

![Landing page](docs/screenshots/steep-landing-top.png)

## Screenshots

| | |
|---|---|
| ![Auth](docs/screenshots/steep-auth.png) | ![Analyzer](docs/screenshots/steep-dashboard-analyze.png) |
| *Business auth* | *AI Analyzer with data-quality grading* |
| ![Insights](docs/screenshots/steep-insights.png) | ![Segments](docs/screenshots/steep-segments2.png) |
| *AI insights, findings & personas* | *K-Means segments with auto-k selection* |

## Features

- **Product landing page** — editorial serif hero with floating product artifacts, features, how-it-works, testimonials, pricing, and FAQ; guest demo mode loads a 3,900-row sample dataset without an account.
- **Business auth** — register/login with bcrypt-hashed passwords and httpOnly session cookies (30 days); all dashboard routes are server-guarded.
- **Universal file analysis** — drag-and-drop upload (25 MB cap) for CSV, TSV, XLSX, and JSON; schema-agnostic: any columns, any rows.
- **Data quality grading** — column-level profiling (type inference: numeric / categorical / date / boolean / id / text), missing-value analysis, IQR outlier detection, and an A–F quality score.
- **Overview dashboard** — KPI cards, histograms, categorical bars, time-series timeline, and a correlation heatmap.
- **AI insights** — LLM-generated executive summary (the peach callout), severity-ranked findings, anomalies, recommendations, and AI-named customer personas via `z-ai-web-dev-sdk`, with structured-JSON validation and retries.
- **Ask AI** — threaded follow-up Q&A grounded on the analyzed dataset.
- **K-Means segmentation** — seeded k-means++ with automatic k selection via silhouette sweep (k = 2…6), donut + segment-colored scatter, and per-segment persona cards with diff bars.
- **Campaign planner** — segment targeting with ROI and break-even modeling across three scenarios.
- **Data explorer** — search, sort, paginate, and export the full table to CSV.
- **Saved datasets** — every analysis is persisted per business account (Prisma + SQLite) and can be reopened or deleted later.

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16 (App Router, standalone output), React 19, TypeScript |
| Styling | Tailwind CSS 4, shadcn/ui, lucide-react, framer-motion |
| Fonts | Source Serif 4 (Signifier substitute) + Inter (Sohne substitute) via `next/font` |
| Database | Prisma ORM + SQLite (`db/custom.db`) |
| Auth | bcryptjs + session-cookie (`src/lib/auth.ts`) |
| Parsing | papaparse (CSV/TSV), xlsx (Excel), native JSON |
| Charts | recharts |
| AI | z-ai-web-dev-sdk (LLM insights + Q&A) |

## Quick Start

Requirements: **Node.js 18+** (or Bun 1.x). No external database needed — SQLite ships in the box.

```bash
# 1. Install dependencies
bun install            # or: npm install

# 2. Configure environment
cp .env.example .env   # DATABASE_URL=file:./db/custom.db

# 3. Set up the database (regenerates the included one if you prefer fresh data)
bun run db:generate    # or: npx prisma generate
bun run db:push        # or: npx prisma db push

# 4. Run the dev server
bun run dev            # or: npm run dev
```

Open **http://localhost:3000**. You will land on the marketing page — click **Try the demo** to explore with the bundled sample dataset, or **Log in** to register a business account and analyze your own files.

> A pre-seeded SQLite database (`db/custom.db`) is included, so the app runs immediately after install; re-running `db:push` recreates it empty.

### Production build

```bash
bun run build
bun run start          # serves the standalone build on port 3000
```

## Project Structure

```
shopsense-3.0/
├── DESIGN.md                     # Steep design system spec (applied)
├── README.md
├── package.json / bun.lock
├── next.config.ts / tsconfig.json / tailwind.config.ts
├── prisma/
│   └── schema.prisma             # User, Session, Dataset models
├── db/
│   └── custom.db                 # Pre-seeded SQLite database
├── public/
│   ├── logo.svg
│   └── data/sample_customers.csv # 3,900-row guest-demo dataset
├── docs/
│   ├── DESIGN-Auros-alternative.md   # Alternate design spec (not applied)
│   └── screenshots/                  # UI screenshots used in this README
└── src/
    ├── app/
    │   ├── layout.tsx / page.tsx / globals.css   # fonts, Steep tokens, SPA shell
    │   └── api/
    │       ├── analyze/route.ts       # POST — multipart file upload + analysis
    │       ├── insights/route.ts      # POST — LLM insights (summary/findings/personas)
    │       ├── ask/route.ts           # POST — follow-up Q&A
    │       ├── datasets/route.ts      # GET/POST — list/save datasets
    │       ├── datasets/[id]/route.ts # GET/DELETE — load/delete dataset
    │       └── auth/{register,login,logout,me}/route.ts
    ├── components/
    │   ├── shopsense/
    │   │   ├── landing.tsx        # marketing page
    │   │   ├── auth-view.tsx      # login/register split panel
    │   │   ├── dashboard.tsx      # workspace shell + 7 tabs
    │   │   ├── charts.tsx         # recharts theming + all chart types
    │   │   ├── insights-panel.tsx # AI summary, findings, personas, Ask-AI
    │   │   └── logo.tsx
    │   └── ui/                    # shadcn/ui primitives
    ├── lib/
    │   ├── analytics/
    │   │   ├── parse.ts           # universal CSV/TSV/XLSX/JSON parser
    │   │   ├── profile.ts         # typing, stats, outliers, quality grade, charts
    │   │   ├── kmeans.ts          # k-means++ + silhouette auto-k
    │   │   └── pipeline.ts        # orchestrator + LLM context builder
    │   ├── auth.ts                # session helpers
    │   ├── db.ts                  # Prisma client
    │   └── types.ts
    └── hooks/
```

## Design System — Steep

The UI implements the uploaded **Steep** style reference (`DESIGN.md`), an editorial "serif analytics on warm paper" system:

- **Palette** — Ink `#17191c`, Paper `#ffffff`, Mist `#f2f2f3`, Fog `#fafafb`, Slate `#777b86`, Ash `#979799`, Smoke `#a3a6af`, with a single chromatic accent: Blush Peach `#fbe1d1` paired with Sienna `#5d2a1a`. Functional good/warn/bad are muted.
- **Typography** — Source Serif 4 at weight 400 for display/headings (44–90px, negative tracking, italic accents); Inter for body/UI at half-step weights.
- **Shapes** — 24px cards, 16px inputs, fully pill-shaped buttons, hairline borders, barely-there shadows; product surfaces presented as floating artifacts.
- **Rationing rule** — one peach accent moment per view (exec summary card, testimonial, expected-scenario row).

All tokens live as CSS variables in `src/app/globals.css` (`@theme`); charts, buttons, cards, and surfaces consume them via Tailwind utilities. The unapplied alternative spec is kept at `docs/DESIGN-Auros-alternative.md`.

## API Reference

| Method | Route | Description |
|---|---|---|
| POST | `/api/analyze` | Multipart upload (≤25MB); parses + profiles + segments; returns full analysis JSON |
| POST | `/api/insights` | LLM executive summary, findings, anomalies, recommendations, personas |
| POST | `/api/ask` | Ask a follow-up question about the active dataset |
| GET / POST | `/api/datasets` | List saved datasets / save current analysis |
| GET / DELETE | `/api/datasets/[id]` | Load / delete a saved dataset |
| POST | `/api/auth/register` | Create business account |
| POST | `/api/auth/login` | Sign in (sets httpOnly session cookie) |
| POST | `/api/auth/logout` | Sign out |
| GET | `/api/auth/me` | Current session user |

## Scripts

| Command | Action |
|---|---|
| `dev` | Next dev server on port 3000 |
| `build` | Production build (standalone) |
| `start` | Run the standalone production server |
| `lint` | ESLint |
| `db:generate` / `db:push` / `db:migrate` / `db:reset` | Prisma database tooling |

## Troubleshooting

- **`DATABASE_URL` errors on first run** — make sure `.env` exists (`cp .env.example .env`); the path is relative to the project root.
- **Port 3000 busy** — `npm run dev -- -p 3001`.
- **Fresh database** — delete `db/custom.db` and run `bun run db:push`; the schema is recreated empty.
- **AI features** — insights and Ask-AI require the `z-ai-web-dev-sdk` runtime to be available; all statistical features work fully offline without it.
