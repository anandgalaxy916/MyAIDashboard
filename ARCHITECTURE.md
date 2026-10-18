# AI Foresight: MVP and future integration

## Present MVP
- Static progressive web app (PWA): `index.html`, `manifest.webmanifest`, `sw.js`, icons.
- Personal data lives in this browser's `localStorage` under `aiforesight-v1`. It is not synchronized or backed up. Clearing site storage removes changes.
- Preloaded cards are intentionally **sample research topics and leads**, not live news or verified open grants.
- The app is installable after deployment to an HTTPS origin (or localhost for development). Opening the file directly is a preview, not a fully installable PWA.
- No account, analytics, tracking, network fetches or API keys in this MVP. External links open official sites.

## Future reference architecture
```
Official sources / RSS / provider APIs
  -> server-side scheduled ingestion (daily)
  -> URL canonicalization + duplicate detection + source verification
  -> OpenAI Responses API (server-side only; structured output)
  -> validation of JSON, citations, dates and opportunity status
  -> Postgres database
  -> authenticated read API and relevance-ranking service
  -> PWA synchronization and optional web-push notifications
```

## Normalized content schema (suggested)
```
Source { id, name, domain, url, type, checked_at }
Item { id, kind: news|opportunity|funding, title, summary,
  category, tags[], published_at, discovered_at, source_ids[],
  canonical_url, region[], impact_score, relevance_score,
  confidence: verified|unverified|hypothesis,
  why_it_matters, next_action, model_version, reviewed_at }
Funding { item_id, program_name, eligibility[], incorporation_required,
  opens_at, deadline_at, timezone, application_url,
  status: open|closed|unknown, verified_at }
UserState { item_id, bookmarked, read, archived, notes, updated_at }
Learning { id, title, description, duration_minutes, completed_at }
Action { id, title, detail, status, due_at, linked_item_ids[], updated_at }
IngestionRun { id, started_at, finished_at, status, items_added,
  sources_checked, error_summary }
```

## Security and accuracy
1. Never embed an OpenAI API key in browser JavaScript. Use a server endpoint with secrets stored server-side.
2. Private access should be authenticated (passwordless or strong login), with HTTPS and a database restricted to one account.
3. Validate and sanitize externally sourced fields; retain authoritative links and exact publish/verification dates.
4. Do not mark funding programs as open without checking their official application page. Preserve status unknown if unverified.
5. Support delete/export of private saved data, backups and a retention policy before syncing.
6. ChatGPT scheduled tasks do **not** automatically provide a direct integration to this app; use a separate server scheduler for reliable ingestion.

## API proposal
- `GET /api/items?kind=news&category=agentic-ai&sort=impact`
- `GET /api/funding?status=open&region=IN`
- `GET /api/briefings/latest`
- `PATCH /api/me/items/:id` for saved/read/notes
- `GET /api/me/actions` and `POST /api/me/actions`
- `POST /api/ask` for grounded Q&A over stored items

## Suggested next build iteration
Use a backend like Supabase (auth + Postgres) and a serverless scheduled worker. Add authenticated sync, import/export, live-source ingestion and source-level provenance. Keep a hard distinction between collected facts and model interpretation.


## v1.1 live implementation
- Netlify Function `feed.mjs` retrieves public RSS server-side on browser request (no keys).
- Future: scheduled ingestion -> canonical item DB -> OpenAI Responses API server-side -> factual/source verification -> authenticated client read and persistence.
- Funding applications should not be labelled open without official application verification.
