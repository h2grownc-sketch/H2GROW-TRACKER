# H2 Grow Tracker — Setup Guide

## Prerequisites
- Node.js 18+
- GitHub account
- Vercel account (free tier)
- Supabase account (free tier)

## 1. Supabase Setup

1. Go to [supabase.com/dashboard](https://supabase.com/dashboard)
2. Create a new project (name: `h2grow-tracker`, region: US East)
3. Go to **SQL Editor** and run the table creation SQL (see `scripts/` folder)
4. Go to **Settings > API** and copy:
   - **Project URL** (e.g., `https://xxxx.supabase.co`)
   - **anon public key** (long `eyJ...` string)

## 2. Local Development

```bash
git clone https://github.com/h2grownc-sketch/h2grow-tracker.git
cd h2grow-tracker
npm install
```

Create `.env.local`:
```
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
NEXT_PUBLIC_PIN_CODE=2024
```

```bash
npm run dev
```

## 3. Deploy to Vercel

1. Push code to GitHub
2. Connect repo to Vercel
3. Set environment variables in Vercel:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `NEXT_PUBLIC_PIN_CODE` (optional, defaults to 2024)
4. Deploy

## 4. Data Migration (from Google Sheets)

If migrating from the old Google Sheets version:

```bash
# Export Google Sheet as CSV, then:
NEXT_PUBLIC_SUPABASE_URL=your_url NEXT_PUBLIC_SUPABASE_ANON_KEY=your_key node scripts/migrate-to-supabase.mjs path/to/export.csv
```
