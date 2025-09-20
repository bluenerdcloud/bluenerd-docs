# Epic: google-ads-automation

Automate Lead Form + Webhook setup via Google Ads API so subscribers never touch Ads UI.

---

## Story #23 — Subscriber OAuth into Google Ads

**Epic:** google-ads-automation

**Problem:** Bluenerd needs access to each advertiser’s Google Ads account without manual copy-paste.

**Solution:** Implement Google OAuth consent flow. Store `refresh_token` + `customer_id` per subscriber.

**Acceptance Criteria:**
- GIVEN a subscriber starts a subscription, WHEN they authorize with Google, THEN Bluenerd securely stores a refresh token and customer_id.
- Token stored in encrypted store (AWS Secrets Manager or DB with KMS).
- Error flows: expired/denied tokens handled gracefully.

---

## Story #24 — Generate tenant secrets & Webhook URL

**Epic:** google-ads-automation

**Problem:** Each subscriber needs their own webhook endpoint + secret.

**Solution:** Auto-generate `tenant_public_id` (UUID) + `webhook_secret`.

**Acceptance Criteria:**
- Tenant row created on subscription activation.
- Webhook URL = `https://api.bluenerd.cloud/leads/google/{tenant_public_id}`.
- Secret key unique per tenant, rotatable.
- Stored securely, not hard-coded.

---

## Story #25 — Create/Update Google Lead Form Asset via API

**Epic:** google-ads-automation

**Problem:** Manual setup in Ads UI doesn’t scale.

**Solution:** Use Google Ads API to create or update a `LeadFormAsset` with webhook delivery configured.

**Acceptance Criteria:**
- GIVEN OAuth token & customer_id, WHEN subscriber signs up, THEN Bluenerd creates/updates a Lead Form asset with:
  - Delivery → Webhook URL = tenant URL.
  - Key/Token = tenant webhook_secret.
- Asset attached to a campaign/ad group (configurable).
- Success confirmed via API response.

---

## Story #26 — Handle API retries & error states

**Epic:** google-ads-automation

**Problem:** Google Ads API calls can fail (quota, invalid perms).

**Solution:** Build retry/backoff & error logs.

**Acceptance Criteria:**
- Retries with exponential backoff on `RESOURCE_EXHAUSTED`.
- Clear error messages stored (e.g., “Invalid scope” / “No permission”).
- Subscriber dashboard shows status (Success, Failed, Needs Re-auth).

---

## Story #27 — Fallback: Poll leads via Ads API (optional)

**Epic:** google-ads-automation

**Problem:** Webhook may not always be feasible.

**Solution:** Implement GAQL query for `lead_form_submission_data`.

**Acceptance Criteria:**
- Scheduled job polls recent leads (<30 days).
- Insert into `opportunities` with `source='google_ads_poll'`.
- Deduplicate against webhook leads using fingerprint.

---

## Story #28 — Subscriber dashboard: “Connect Google Ads”

**Epic:** google-ads-automation

**Problem:** Subscriber needs a simple UX to connect.

**Solution:** Add UI flow: “Connect my Google Ads” → OAuth → Bluenerd provisions Lead Form asset automatically.

**Acceptance Criteria:**
- Shows Webhook URL + Key (read-only, no copy/paste required).
- Shows status (Connected, Error, Pending).
- “Send test lead” button posts a dummy lead to Bluenerd endpoint and confirms it appears on Leads Screen.

---

## 📌 Delivery sequence (suggestion)
1. #23 (OAuth) → #24 (tenant secrets)
2. #25 (create/update lead form asset)
3. #26 (error handling)
4. #28 (subscriber dashboard flow)
5. #27 (polling fallback — later)
