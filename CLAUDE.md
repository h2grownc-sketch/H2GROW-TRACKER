# H2 Grow Tracker — Project Guidelines

## Product Context

This app is the internal operating system for H2 Grow LLC, a hydroseeding and land management business. It tracks jobs from first contact through completion and follow-up.

## Primary Users

- **Owner / Estimator** — creates leads, builds quotes, reviews pipeline
- **Project Manager / Scheduler** — assigns work, manages the weekly schedule, monitors overdue items
- **Field Crew / Operator** — checks off completed steps from the field on mobile
- **Office Admin** — data entry, follow-up tracking, reporting

## Business Goals

- Track leads from first contact to completed work
- Manage hydroseeding and land management workflows separately
- Track soil sampling and lab result status
- Move approved work into scheduling quickly
- Give the PM clear visibility into what is waiting, ready, scheduled, in progress, and overdue
- Reduce missed follow-ups
- Improve quoting, scheduling, and operational visibility

## Critical Preservation Rule

**The Ops tab and Estimating function are business-critical.**

- Preserve ALL existing job data, fields, calculations, filters, reports, historical records, and dependencies
- Do NOT casually rename, delete, or restructure job-related data
- Any schema changes MUST preserve backward compatibility and include a safe migration path
- All checklist keys and stage calculations must produce identical results for existing data after any change

## Tech Stack

- **Frontend**: Next.js 14 + React 18
- **Backend**: Supabase (PostgreSQL)
- **Deployment**: Vercel + GitHub (h2grownc-sketch/h2grow-tracker)
- **Auth**: PIN code (env var NEXT_PUBLIC_PIN_CODE, default 2024)
- **Map**: Leaflet via CDN (no npm package)
- **PWA**: Installable on mobile via manifest.json

## App Tabs

1. **Dashboard** — KPI strip + operational sections (Needs Attention, Your Action, Waiting On Others, Ready to Move, This Week, Recently Added)
2. **Jobs** — compact job cards with search + smart filter dropdown, priority sections (overdue/active/completed/dead)
3. **Schedule** — week calendar with deposit status
4. **Map** — Leaflet map with 50+ NC cities, geocoding from city + address fallback, filters
5. **Calc** — materials calculator with tank counts, line items, Supercast order generator
6. **Ref** — quick reference accordions (inquiry script, loading chart, T120/333G procedures, suppliers)
7. **Ops** — equipment checklists with progress bars (daily/weekly/monthly for T120 and 333G)

## Core Workflows

### Hydroseeding Pipeline (17 steps with soil, 14 without)
1. Initial Contact
2. Consultation Needed
3. Consultation Complete
4. Soil Samples Collected (if soilSamplesRequired)
5. Soil Samples Mailed (if soilSamplesRequired)
6. Results Received (if soilSamplesRequired)
7. Hydroseed Quote Sent
8. Site Prep Quote Sent (if requiresSitePrep)
9. Customer Approved
10. 50% Deposit Received
11. Materials Ordered
12. Job Scheduled
13. Job Complete
14. Care Instructions Sent
15. 14-Day Follow-Up
16. 30-Day Follow-Up
17. 90-Day Follow-Up

### Land Management / Simple Pipeline (9 steps)
1. Initial Contact
2. Consultation Needed
3. Consultation Complete
4. Quoted
5. Customer Approved
6. 50% Deposit Received
7. Scheduled
8. Complete
9. 3-Day Follow-Up

## Data Schema (Supabase PostgreSQL — snake_case columns)

### Job Fields (29 total)
```
id, customer_name, phone, email, address, city, state,
service_type, sqft, source, notes, quote_amount, site_prep_amount,
soil_test_number, sample_mailed_date, quote_sent_date,
scheduled_date, spray_date, date_created,
checks (JSONB), county, assigned_to, estimate_status,
require_site_prep (bool), soil_samples_required (bool),
is_dead (bool), dead_reason, photos,
created_at, updated_at
```

### Checklist Keys (19 total, inside `checks` JSONB)
```
contacted, consultationNeeded, consultationComplete,
siteVisit, soilCollected, soilMailed, resultsReceived,
quoteSent, sitePrepQuoteSent, approved, depositReceived,
materialsOrdered, scheduled, jobComplete, careSent,
followUp3, followUp14, followUp30, followUp90
```

### Boolean Toggles
- `soilSamplesRequired` — shows/hides soil steps in hydro checklist (default: true)
- `requiresSitePrep` — shows/hides site prep quote field + checklist item (default: false)
- `isDead` — marks job as dead/lost lead

## Service Types
Hydroseeding, Forestry Mulching, Site Prep / Grading, Drainage, Erosion Control, Food Plot, Skid Steer Work, Other

## Lead Sources
RingCentral, Website, Referral, Repeat, Facebook, Google, Other

## Engineering Rules

- **Analyze before coding** — read existing code and understand dependencies before making changes
- **Protect existing workflows** — never break what's working
- **Preserve job data** — schema changes are additive only unless explicitly approved
- **Reuse components** — prefer extending existing code over creating parallel implementations
- **Avoid breaking changes** — new fields default to safe empty values for existing records
- **Add loading, empty, and error states** — every view should handle all three
- **Validate forms** — required fields enforced, types checked
- **Prefer maintainable code over hacks** — clean component structure, extracted utilities
- **Test after every phase** — verify all calculations, views, and data integrity

## File Structure

```
app/page.js              Main app — PIN auth, 7 tabs, state management
app/layout.js             Root layout, metadata, PWA
app/globals.css           All styles
components/               12 component files
lib/jobUtils.js           Business logic (stages, urgency, alerts, checklists)
lib/supabase.js           Supabase client + CRUD with field mapping
archive/sheets.js         Legacy Google Sheets API (not used)
archive/google-apps-script.js  Legacy Apps Script backend (not used)
public/logo.jpg           H2 Grow logo
public/manifest.json      PWA manifest
```
