# Life Planner

A single-file personal finance planner. Budgeting, savings, and spending breakdowns; salary, mortgage, and student‑loan trackers; a UK tax‑year deduction calculator; per‑account bank allocation; and money‑flow visualisations — all in one HTML file, with optional cloud sync.

## Use it

Open `index.html` in any modern browser, or visit the hosted page.

Your data is saved **locally in your browser** (localStorage) and the app works fully offline. Sign in from the account button in the top‑right to **back up and sync across devices**.

## Features

- **Accounting dashboard** — income, deductions, expenses (with nested sub‑items and rounding), savings, and a spending budget, with live per‑week / per‑day figures.
- **Money Breakdown** — an interactive money‑flow Sankey plus gross‑income and allocation donuts, with a hide‑figures toggle.
- **Bank Allocation** — assign each expense, saving, spending budget, and rounding buffer to bank accounts (with splitting) and see what to set aside in each account.
- **Trackers** — salary vs. inflation, mortgage balance (with payoff projection), and student‑loan payoff.
- **Tax Calculator** — estimate Income Tax, National Insurance, Plan 2 student loan, and pension for a chosen (editable) tax year.
- **Cloud sync** — optional, via Supabase; local‑first with the cloud as the source of truth once signed in.
- Light/dark theme, JSON export/import backups, undo/redo, and print‑friendly output.

## Hosting on GitHub Pages

This is a static site — no build step.

1. Push this repo to GitHub.
2. **Settings → Pages → Deploy from a branch → `main` / `/ (root)`**.
3. The site publishes at `https://<username>.github.io/<repo>/`.

## Cloud sync (Supabase)

Sync is powered by [Supabase](https://supabase.com): email/password auth plus a single per‑user row in a `planner_states` table, isolated by **row‑level security (RLS)**. The project URL and the **public anon key** are baked into the page (both are safe to expose — RLS is what protects the data). You can point a copy at a different project from **Configuration → Cloud Sync → Change project settings**.

Table used:

```sql
create table public.planner_states (
  user_id    uuid primary key references auth.users(id) on delete cascade,
  data       text not null default '',
  updated_at timestamptz not null default now(),
  version    integer not null default 1
);
alter table public.planner_states enable row level security;
create policy "own row - select" on public.planner_states for select using (auth.uid() = user_id);
create policy "own row - insert" on public.planner_states for insert with check (auth.uid() = user_id);
create policy "own row - update" on public.planner_states for update using (auth.uid() = user_id);
```

## Privacy

Local‑first: nothing leaves your browser unless you sign in. Cloud sync is optional and only active while signed in.

## License

[MIT](LICENSE)
