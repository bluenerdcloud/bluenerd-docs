
# Privacy-First Lead Capture Flow (Bluenerd Test Plan)

## 🧠 What You're Thinking About

You're considering:
- Privacy-conscious users who might not submit a Google lead form because they don’t want to reveal their name, email, or phone number.
- Offering anonymous sign-ins via Google, where the advertiser doesn’t see their PII but the system still captures lead intent.
- Whether Google Ads already supports this kind of anonymous lead capture (it doesn’t, not in the way you’re describing).
- Whether to build this feature into Bluenerd to give advertisers access to both types of leads: anonymous and full-PII.

This is solid customer empathy and product innovation thinking—but you must avoid overbuilding before testing.

---

## ✅ What Google Ads Actually Allows

- **Google Ads Lead Forms** do **not** support anonymous submissions. The user is logged into Google, and advertisers always get whatever PII the form requests.
- You can reduce friction by limiting the fields (e.g., name + phone only), but full anonymity is not supported.
- Google won't allow leads to hide from the advertiser entirely—it's against their attribution model.

Your idea—to offer anonymous intent capture and deferred opt-in—is not native to Google, which opens an opportunity for **Bluenerd to innovate.**

---

## ✅ Should You Build It?

### Better Framing:
> Is “privacy-aware leads not converting” a *major bottleneck* right now?

If you're unsure, you’re not overthinking—you’re underscoping. Avoid the trap of solving unvalidated edge cases.

---

## 🎯 What to Advertise in the A/B Test

**Test Offer:**  
> “Free Website + Google Ads Audit for Small Businesses — No Signup Required”

**Targets:**  
- Local services (e.g., med spas, wrap shops, tradespeople)
- Small e-commerce or online service providers

**What They Do:**  
- Submit either:
  - Full lead form via Google
  - Anonymous form via Bluenerd landing page

**What You Do:**  
- Provide audit (on-screen or via opt-in)
- Track engagement from anonymous vs full leads

---

## ✅ A/B Test Structure

| Test | Lead Capture Method | What Happens | Where | PII? | Goal |
|------|---------------------|--------------|-------|------|------|
| **A: Google Lead Form** | Native form in ad | Autofills name/email | Google-hosted | ✅ Yes | Baseline |
| **B: Bluenerd Landing Page** | Ad → Your form | Optional anonymity | bluenerd.cloud | ⚠️ Optional | Test privacy-first capture |

---

## 🧠 Do You Require Google Login?

| Scenario | Login Required? | Why? |
|----------|------------------|------|
| Google Lead Form | ✅ Yes | Autofills from account |
| Bluenerd Form | ❌ No | Optional fields, anonymous allowed |
| Later Features | 🔄 Optional | Useful for saved quotes, deeper funnel steps |

---

## ✅ Anonymous Option Details

### Minimum Fields (Bluenerd-hosted):
- `website_url` (required)
- “Stay anonymous” checkbox
- Optional: name, email, phone (disabled if anonymous selected)

### Data Stored:

```json
{
  "lead_id": "4fd2a7e1-6d28-4a1a-bb5a-cae5d3098eab",
  "website_url": "https://example.com",
  "is_anonymous": true,
  "created_at": "2025-09-21T17:42:00Z"
}
```

---

## ✅ Anonymous Result Delivery (Option 2)

- Generate a UUID: `audit.bluenerd.cloud/anon/4fd2a7e1-6d28-4a1a-bb5a-cae5d3098eab`
- Redirect the user to this page after form submission
- Advise them to **bookmark** the link
- Optionally, drop a cookie for easier return
- Show status:
  - ⏳ Pending
  - ✅ Complete with findings
- Optional follow-up:  
  > “Want a deeper audit? Drop your email (optional)”

---

## ✅ Key Benefits

| Feature | Value |
|---------|-------|
| No login | Low friction |
| Unique link | Private access |
| Bookmark/cookie | Easy return |
| Soft opt-in | Converts privacy-first leads later |
| Differentiator | Builds trust and brand identity |

