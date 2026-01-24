# Customer Journeys — Free vs Paid

**Owner:** Omar  
**Status:** Draft → Finalize flows + identify “longest pole” (10DLC/TFN compliance)  
**Last updated:** 2025-10-23

---

## 1) Purpose
Define and lock the end-to-end customer journeys for **Start Free** and **Get Pro** so engineering, compliance, and go-to-market move in sync. Call out the **longest pole** explicitly so we start it first.

## 2) Definitions
- **Free plan:** No SMS features. Email-only onboarding. Website capture via simple form/lead inbox.
- **Paid plan (Pro):** Includes SMS/voice touchpoints. Requires 10DLC (A2P) or Toll-Free Number (TFN) verification.

## 3) Assumptions
- Cognito is the IdP. First-time users should return to the Setup Wizard after sign-up.
- One **prod** API Gateway stage for now.
- No shared phone numbers (10DLC rules require identified sender).
- Billing handled by Stripe (placeholder) with monthly subscription for Paid.

## 4) Out of Scope (for this story)
- In-app analytics dashboards
- Multi-tenant billing proration edge cases
- International messaging

---

## 5) Roles
- **Buyer (Owner-operator)**: selects Free or Paid, completes setup.
- **Bluenerd App**: onboarding wizard, settings, lead inbox.
- **Compliance**: 10DLC/TFN artifacts and approvals.
- **Support**: human contact for rejections/edits.

---

## 6) Journey A — **Free** (no SMS)

**Goal:** Fast, no-friction onboarding to capture leads via web form/email only.

**Flow:**
1. **Marketing site** → Click **Start Free**
2. **Collect details page** (name, email, business name) → **Continue**
3. **Cognito sign-up** → email verify → **return to Setup Wizard**
4. **Setup Wizard**
   - Step 1: Business Identity (Name, Short Description, Website)
   - Step 2: Lead Capture (embed form snippet, email forwarding)
   - Step 3: Team (optional invites)
   - Step 4: Owner Email (pre-filled, editable; uniqueness re-check on submit)
5. **Settings page preview** (works without backend data; shows placeholders)
6. **Success** → **Dashboard/Leads** (no SMS features visible)
7. **Header** reflects **Sign-Out** when authenticated

**Acceptance Criteria (Free):**
- ✅ No SMS or phone number requested anywhere
- ✅ Owner email uniqueness checked at final submit
- ✅ Settings preview renders with local defaults (no wiring needed)
- ✅ After login, header shows **Sign-Out** and clicking it clears cookies/session

---

## 7) Journey B — **Paid (Pro)** with messaging

**Goal:** Enable SMS outreach. Requires compliance artifacts pre-send.

**Flow:**
1. **Marketing site** → Click **Get Pro**
2. **Collect details page** → **Continue**
3. **Cognito sign-up** → email verify → **return to Setup Wizard**
4. **Setup Wizard**
   - Step 1: Business Identity (adds **Legal Business Name**, EIN if available)
   - Step 2: Use Cases (primary messaging purpose + sample messages)
   - Step 3: Opt-in/Opt-out/Help keywords (STOP/HELP text confirmed)
   - Step 4: Links (Privacy Policy, Terms, Website)
   - Step 5: **Business Identity Phone Number** (explain: used when a lead provides no contact number; required)
   - Step 6: Choose **10DLC vs TFN** path (see Longest Pole below)
   - Step 7: Billing & Plan confirmation
5. **Compliance submission** (auto-generate package from wizard)
6. **Number assignment** (after approval; attach to Messaging Service)
7. **Go-live checks** (sender ID, templates, unsubscribe flow working)
8. **Dashboard/Leads** with SMS actions enabled

**Acceptance Criteria (Paid):**
- ✅ Wizard gathers all compliance fields and validates presence
- ✅ Submission artifact is generated and stored (PDF/JSON bundle OK)
- ✅ No outbound SMS until **Approved**
- ✅ After approval, a number is attached and visible in Settings

---

## 8) **Longest Pole** — 10DLC/TFN Compliance (start first): See 10DLC/TFN Compliance document

> The compliance/verification step has the longest external lead time and risk. Start this work **before** UI polish.

**What must be captured:**
- Legal Business Name, EIN (or equivalent), address, website
- Brand use case(s) and campaign descriptions
- **Sample messages** (onboarding, reminders, follow-ups) with placeholders
- **Opt-in method** (how users consent), **Opt-out** keywords (STOP), **Help** keywords (HELP)
- Support contact (email/URL), **Privacy Policy** link, **Terms** link
- Message frequency expectations and fee disclaimers if any

**Choose a path:**
- **A) 10DLC (Local A2P):** Register brand + campaign, approve templates, then assign local number(s).
- **B) TFN (Toll-Free):** Submit TFN verification details; approval required for higher throughput.

**Go/No-Go Gate:**
- 🚧 **Block sending** until status **Approved** (either 10DLC or TFN).  
- UI must show **Pending/Approved/Rejected** with next actions.

**Artifact Checklist (to generate on submit):**
- [ ] JSON payload with all fields (brand, use cases, samples, opt-in/out text, links)
- [ ] Human-readable **PDF** summary for review
- [ ] Stored in `/compliance/{tenantId}/{date}/` (S3), reference in DB

---

## 9) UX Notes & Copy Requirements
- No shared numbers; every sender must be identified (compliance).
- Inline helper text under **Business Identity Phone Number**:
  > “Used when a lead doesn’t provide a number. Required to route callbacks/messages.”
- Empty/state placeholders so Settings preview renders before wiring.

---

## 10) Engineering Tasks (split later into issues)
- Frontend: Setup Wizard steps (Free vs Paid branches), validations, preview mode.
- Backend: Compliance submission endpoint → store JSON/PDF, status tracking.
- Messaging: Number assignment post-approval; block UI actions until approved.
- Auth: After sign-up, redirect back to wizard (first-time users).
- Header: Sign-In ↔ Sign-Out toggle; sign-out clears cookies.

---

## 11) Test Notes
- Free path completes with no phone asks and no SMS UI.
- Paid path blocks SMS until a mocked **Approved** status is returned.
- Negative tests: missing Privacy/Terms, missing samples, rejected compliance → clear remediation prompts.

---

## 12) Open Questions
- Will initial Bluenerd dogfooding use TFN or 10DLC?
- Do we support number inventory now or attach post-approval only?
- Billing proration for mid-cycle plan upgrades?

---

## 13) Done When
- Both journeys render as described and pass Acceptance Criteria.
- Compliance artifact is generated from the Paid wizard and saved.
- “Longest pole” is clearly marked and kicked off first.

### Channel Decision (locked)

- **Bluenerd dogfooding:** **TFN (toll-free)**
  - Reason: single sender, simpler initial approval package, one number visible in demos.
- **Paid clients:** **10DLC per client/brand** (no shared numbers)
  - Reason: carrier rules require identified sender; local presence when needed; client-owned compliance.

**Operational rules**
- No outbound messaging until status **Approved** (TFN or 10DLC).
- Number attaches only **after** approval to the client’s Messaging Service.
- UI must show **Pending / Approved / Rejected** with remediation steps.


