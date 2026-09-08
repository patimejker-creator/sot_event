-- VETOC backend hardening for Supabase/Postgres.
-- Apply in Supabase SQL Editor AFTER checking your existing policies.
-- This file intentionally does not contain any secret/service-role key.

-- Public table: allow public reads only for published content.
alter table if exists public.vetoc_content enable row level security;

drop policy if exists "Public can read published VETOC content" on public.vetoc_content;
create policy "Public can read published VETOC content"
on public.vetoc_content
for select
to anon, authenticated
using (is_published = true);

-- Authenticated users can manage content. Use a dedicated Supabase admin account
-- for the admin page; do not use the service_role key in the browser.
drop policy if exists "Authenticated can manage VETOC content" on public.vetoc_content;
create policy "Authenticated can manage VETOC content"
on public.vetoc_content
for all
to authenticated
using (true)
with check (true);

-- Storage: public read, authenticated write/delete.
-- If the bucket name differs, replace vetoc-media with your real bucket.
drop policy if exists "VETOC media public read" on storage.objects;
create policy "VETOC media public read"
on storage.objects
for select
to public
using (bucket_id = 'vetoc-media');

drop policy if exists "VETOC media authenticated upload" on storage.objects;
create policy "VETOC media authenticated upload"
on storage.objects
for insert
to authenticated
with check (bucket_id = 'vetoc-media');

drop policy if exists "VETOC media authenticated update" on storage.objects;
create policy "VETOC media authenticated update"
on storage.objects
for update
to authenticated
using (bucket_id = 'vetoc-media')
with check (bucket_id = 'vetoc-media');

drop policy if exists "VETOC media authenticated delete" on storage.objects;
create policy "VETOC media authenticated delete"
on storage.objects
for delete
to authenticated
using (bucket_id = 'vetoc-media');

-- Optional database hygiene: prevent anonymous role from changing table data.
revoke insert, update, delete, truncate on public.vetoc_content from anon;
revoke insert, update, delete on storage.objects from anon;
