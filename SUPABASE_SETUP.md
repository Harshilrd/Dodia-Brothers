# Supabase setup (for multi-device shared database)

To use one shared database from multiple devices (phones, computers), create a free Supabase project and add the tables below. Then set `SUPABASE_URL` and `SUPABASE_ANON_KEY` in `index.html` (near the top of the script).

## 1. Create a Supabase project

1. Go to [supabase.com](https://supabase.com) and sign up / log in.
2. Create a new project (e.g. "metal-cutting").
3. In the project: **Settings → API**:
   - Copy **Project URL** → use as `SUPABASE_URL`.
   - Copy **anon public** key → use as `SUPABASE_ANON_KEY`.

## 2. Create tables

In Supabase: **SQL Editor → New query**, paste and run:

```sql
-- Clients (master list: name, address, mobile, GST number)
create table if not exists clients (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  address text,
  mobile text,
  gst_number text,
  created_at timestamptz default now()
);

-- Jobs (one per saved record; links to client)
create table if not exists jobs (
  id uuid primary key default gen_random_uuid(),
  client_id uuid references clients(id) on delete set null,
  original_length numeric not null default 0,
  original_width numeric not null default 0,
  original_thickness numeric not null default 0,
  original_quantity integer not null default 1,
  total_weight numeric not null default 0,
  gauge_cutting boolean default false,
  gauge_size numeric,
  total_cuts integer,
  gauge_total_weight numeric,
  rs_per_kg numeric not null default 0,
  rs_per_cut numeric not null default 0,
  total_cost numeric not null default 0,
  created_at timestamptz default now()
);

-- Line items (each cutting plate size per job)
create table if not exists job_line_items (
  id uuid primary key default gen_random_uuid(),
  job_id uuid references jobs(id) on delete cascade,
  cut_length numeric not null default 0,
  cut_width numeric not null default 0,
  cut_thickness numeric not null default 0,
  quantity integer not null default 1,
  weight_per_piece numeric not null default 0,
  line_total_weight numeric not null default 0,
  sort_order integer not null default 0
);

-- Allow anonymous read/write for the app (anon key)
alter table clients enable row level security;
alter table jobs enable row level security;
alter table job_line_items enable row level security;

create policy "Allow all for anon" on clients for all using (true) with check (true);
create policy "Allow all for anon" on jobs for all using (true) with check (true);
create policy "Allow all for anon" on job_line_items for all using (true) with check (true);
```

## 3. Set credentials in the app

In `index.html`, find (near line 220):

```javascript
var SUPABASE_URL = ''; // Set your Supabase project URL
var SUPABASE_ANON_KEY = ''; // Set your Supabase anon/public key
```

Replace with your values, for example:

```javascript
var SUPABASE_URL = 'https://xxxxx.supabase.co';
var SUPABASE_ANON_KEY = 'eyJhbGc...your-anon-key...';
```

Save and redeploy. All devices using this app will then read and write to the same database.

**Without Supabase:** If you leave both empty, the app uses IndexedDB in the browser (data is per device only).
