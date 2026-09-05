# MR.ONE_Oto — Legacy AppDeploy Recovery Audit

Date: 2026-09-05
Source app: `mr-one-content-studio-publisher-tgt77z`
Recovered AppDeploy version: `1788576385486`

## Purpose
Preserve useful legacy implementation knowledge in an isolated recovery branch before any AppDeploy cleanup/disconnection. This document is NOT the production blueprint and must NOT be merged into `main` automatically.

## Useful components to retain
1. **Supabase as persistence target** — legacy backend already used the existing Supabase project and the secure `SUPABASE_PUBLISHABLE_KEY` secret. This aligns with the locked MR.ONE_Oto architecture where Supabase is the source of truth.
2. **Server-side Supabase access helper** — the backend centralized REST calls through a helper, rather than exposing the key in the browser.
3. **Media upload flow** — browser converts selected files to data URLs, backend decodes them, uploads to Supabase Storage, and returns storage metadata. The concept is reusable, but the bucket/path/public-URL contract must be replaced with the current MR.ONE_Oto storage contract.
4. **Separate IMAGE and VIDEO routes** — the UI explicitly keeps the two workflows separate.
5. **Maximum-five input handling** — UI limits selection to five and backend validates extraction/media counts. The current foundation must enforce the locked rule exactly and independently of legacy assumptions.
6. **AI extraction guardrail** — extraction prompt explicitly says to use only visible screenshot facts and not invent missing vehicle information. This principle is directly reusable.
7. **AI copy guardrail** — copy generation is instructed to use only reviewed vehicle facts and treat WhatsApp as contact metadata, not a vehicle fact. This principle is directly reusable.
8. **Reset separation** — legacy UI already distinguishes Reset Konten from Reset Produk, matching the locked blueprint's separation.
9. **Failure notices** — upload and AI errors are surfaced to the user instead of being silently ignored.
10. **Reload/persistence UX** — dashboard reloads posts from the backend and an existing post can reload product/media state.

## Components NOT to carry forward as-is
1. `products.category` is referenced by legacy UI/backend, but the current Supabase schema does not have a `category` column. Do not reintroduce it.
2. Legacy upload targets bucket `mr-one-originals`; current MR.ONE_Oto target is `mr-one-oto-originals`.
3. Legacy upload returns `public_url: null`; Buffer requires a stable public HTTPS media URL at publish time. Current implementation must use the locked storage contract.
4. Legacy source uses a direct REST backend and AppDeploy secrets. AppDeploy is a recovery/development dependency, not the MR.ONE_Oto runtime source of truth.
5. Legacy `persist` deletes/reinserts `media_assets`, which is risky for robust transactional behavior and should not be copied blindly.
6. Legacy UI contains dashboard placeholder metrics (`7`, `4`, `2`) and hard-coded platform labels. These are presentation placeholders, not production truth.
7. Legacy VIDEO flow marks processing complete after save without a real video-content processing stage. Do not treat this as production video generation.
8. Legacy source contains no real Buffer publishing implementation. Do not infer that publishing is operational from this source.

## Security/architecture finding
The legacy AppDeploy source is valuable as a recovery reference, but it is not the current MR.ONE_Oto implementation. The current locked architecture remains authoritative: Supabase = source of truth, Buffer = publisher, AI provider modular, and review gates remain mandatory.

## Recovery rule
Keep this branch isolated. Do not merge into `main` unless a specific legacy component is deliberately reviewed and adapted to the locked MR.ONE_Oto blueprint.
