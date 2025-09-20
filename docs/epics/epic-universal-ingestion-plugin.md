# Epic: universal-ingestion-plugin

> Drop-in client + server bits so **any** frontend can send leads to Bluenerd securely (no secrets in the browser). Single `/leads` endpoint stays the source of truth.

---

## Problem
Non-Google sources shouldn’t require a custom frontend or leak secrets. We need a simple, portable way for **any** site/app/CMS to POST leads to Bluenerd without exposing `SHARED_SECRET`.

## Goals
- Zero secrets in browsers.
- Works with **any** frontend (plain JS, React, WordPress, Webflow, etc.).
- Reuse the existing **`POST /leads`** Lambda.
- Tenant-aware, rate-limited, auditable.

## Out of scope (for this epic)
- Full-blown campaign analytics & attribution UI (captured as meta but not visualized here).
- Google Ads automation (belongs to epic: `google-ads-automation`).

---

## Architecture (high level)
1. **Short-lived client token** (JWT) is issued server-side to a tenant.
2. Frontend uses the token to call **`POST /leads`** (no shared secret in client).
3. `/leads` verifies token **OR** legacy headers (`x-google-key` / `x-shared-secret`), then normalizes + inserts.

---

## API Additions (this epic)

### 1) `POST /leads/token` — Issue a short-lived client token
- **Auth:** Admin/session for tenant; _not_ public.
- **Body (server->server or admin session):**
  ```json
  { "tenant_id": "uuid", "ttl_seconds": 180, "origins": ["https://example.com"] }
  ```
- **Response:**
  ```json
  { "token": "<JWT>", "expires_in": 180 }
  ```
- **Claims in JWT:** `{ tid, origins, iat, exp, jti }`
- **Signing:** HS256 with KMS-backed secret (rotatable).

### 2) `POST /leads` — Accept JWT as an alternative to headers
- **Auth (any one of):**
  - `Authorization: Bearer <JWT>`  (new)
  - `x-google-key: <GOOGLE_WEBHOOK_KEY>`  (existing)
  - `x-shared-secret: <SHARED_SECRET>`     (existing)
- **Body:** `{ name, email, phone, ...meta }` or Google webhook payload (already supported).
- **Behavior:** Verify auth → normalize → insert → 200.

---

## Stories

### Story 1 — Token issuance endpoint
**Why:** Never expose `SHARED_SECRET` to browsers.  
**AC:**
- `POST /leads/token` issues HS256 JWT with `tid` (tenant), `exp` (≤ 5m), `origins` array.
- Only authenticated admin/server can call it.
- Tokens logged by `jti` (no PII), revocable list cached (optional).
- Secrets kept in AWS Secrets Manager / KMS; rotation supported.

### Story 2 — `/leads` accepts `Authorization: Bearer <JWT>`
**Why:** Let clients use short-lived tokens instead of secrets.  
**AC:**
- Lambda verifies token signature, expiry, and that `Origin` (or `Referer`) is in claims’ `origins` (when present).
- On success, treat as `via="jwt"` and proceed to normalization + insert.
- On failure, 401 with a terse reason code (expired, bad signature, origin mismatch).

### Story 3 — JS SDK (`@bluenerd/leads`)
**Why:** One-line adoption for most apps.  
**AC:**
- `submitLead({ name, email, phone, meta })` returns `{ ok, id }` or throws.
- Retries with exponential backoff (≤ 3 attempts).
- No secret in code; accepts `token` param or reads from meta-tag `data-bluenerd-token`.
- Tree-shakeable, no framework lock-in. Provide a small React hook wrapper `useBluenerdLead()`.

### Story 4 — Web component (no build tools)
**Why:** Copy-paste `<script>` for CMS/static sites.  
**AC:**
- `<script src="https://cdn.bluenerd.cloud/leads.js"></script>` exposes `BluenerdLeads.init({ token, onSuccess, onError })`.
- Provides a `window.bluenerdSubmit(data)` helper.
- Sanitizes inputs; blocks obviously invalid email/phone before POST.

### Story 5 — CORS, rate limiting, anti-abuse
**Why:** Secure, reliable public ingestion.  
**AC:**
- CORS allowlist pulled from tenant config (`origins`).
- API Gateway / Lambda rate limits per tenant (429 with backoff headers).
- Optional hCaptcha/Turnstile hook on `/leads/token` or `/leads` (config flag).
- CloudWatch metrics: `LeadsAccepted{via}`, `LeadsRejected{reason}`.

### Story 6 — Docs & samples
**Why:** Make it trivial to integrate.  
**AC:**
- Markdown quickstart for: plain HTML, React, Next.js, WordPress.
- Example code snippets with token lifecycle (server issues token → client uses token).
- Diagrams: request flow, token validation, error handling.

---

## Implementation notes

- **JWT Claim checks** (in `/leads` Lambda):
  - Verify signature & `exp`.
  - If `origins` in claims, compare to `Origin` header (fallback to `Referer` hostname).
  - Extract `tid` for partitioning (`tenant_id` in insert) when you add multi-tenancy.

- **Backwards compatibility:**
  - Keep accepting `x-google-key` and `x-shared-secret` for existing integrations.
  - Prefer JWT when present; set `via` accordingly (`"jwt" | "google" | "site_form"`).

- **Insert schema (current):**
  - `lead_source` → derive from `via`.
  - `lead_data` → `{ normalized, raw }`
  - `received_at` → NOW()
  - (Future) Add typed columns/dedupe as in Story #18.

- **Security posture:**
  - Tokens are short-lived, scoped to tenant & origins.
  - No long-lived secret in browsers.
  - Rotate signing keys; support key id `kid` in JWT for rollovers.

---

## Delivery order (suggested)
1) Story 1 — Token issuance endpoint  
2) Story 2 — `/leads` JWT validation path  
3) Story 5 — CORS + rate limiting  
4) Story 3 — JS SDK  
5) Story 4 — Web component  
6) Story 6 — Docs & samples

---

## Test matrix (essentials)
- ✅ Valid JWT, allowed origin → 200
- ❌ Valid JWT, wrong origin → 401
- ❌ Expired JWT → 401
- ❌ Bad signature → 401
- ✅ Google `x-google-key` payload → 200
- ✅ Site form `x-shared-secret` → 200
- ❌ Missing auth → 401

---

## Rollout
- Keep `/leads` stable; add JWT path without breaking Google/site-form flows.
- Dogfood with bluenerd.cloud demo, then publish SDK & docs.
- Add hCaptcha toggle for public sites if abuse detected.
