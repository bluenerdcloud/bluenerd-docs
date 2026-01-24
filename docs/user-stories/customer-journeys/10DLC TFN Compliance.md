### 8.a Compliance Data Block (fill these now)

**Brand / Business**
- Legal Business Name: **TODO**
- EIN (or Tax ID): **TODO**
- Business Address: **TODO**
- Website: https://bluenerd.cloud (update if different)
- Support Email: **support@bluenerd.cloud** (or TODO)
- Support URL: **https://bluenerd.cloud/support** (or TODO)
- Privacy Policy URL: **https://bluenerd.cloud/privacy** (or TODO)
- Terms of Service URL: **https://bluenerd.cloud/terms** (or TODO)

**Primary Use Cases** *(select at least one and keep copy tight)*  
- Lead capture follow-up (first contact after web inquiry)  
- Appointment reminders/confirmations  
- Service updates (e.g., “job ready for review”)  

**Message Frequency**
- “Message frequency varies. Standard message & data rates may apply.”

**Keywords & Required Copy**  
- Opt-out: “Reply STOP to opt out.”  
- Help: “Reply HELP for help.”  
- Contact: “Support: support@bluenerd.cloud” (or your URL)

**Opt-in Method(s)** *(check all that apply)*  
- ✅ Website form with explicit consent checkbox and TCPA disclosure  
- ✅ Existing customer provides mobile during service intake  
- ☐ Paper form / POS consent  
- Notes: **TODO** (where consent text appears on your site/app)

**Sample Messages** *(edit placeholders in braces)*  
1) **First contact after lead**  
   “Hi {first_name}, it’s {business_name}. Thanks for your inquiry about {service}. Want to schedule a quick call? Reply 1 for Yes, 2 for Later. Reply STOP to opt out, HELP for help.”  
2) **Appointment confirmation**  
   “Appt confirmed for {date} {time} with {rep_name}. Reply C to cancel, R to reschedule. Reply STOP to opt out, HELP for help.”  
3) **Follow-up**  
   “Quick check-in about your {service} quote. Need any changes? Reply YES to chat now. Reply STOP to opt out, HELP for help.”

**Channel Choice**
- **Preferred path:** **TODO** (choose one)
  - 10DLC (local A2P) — requires Brand + Campaign registration and template approval
  - TFN (toll-free) — requires TFN verification for higher throughput

**Blocking Rule**
- No outbound messaging until status **Approved**; UI shows **Pending/Approved/Rejected** and next steps.

**Artifact Outputs (on submit)**
- JSON saved to: `s3://{bucket}/compliance/{tenantId}/{yyyy-mm-dd}/submission.json`
- PDF summary saved to: `s3://{bucket}/compliance/{tenantId}/{yyyy-mm-dd}/submission.pdf`
- DB record with status + S3 keys

### Channel Choice (policy)
- **Bluenerd internal/dogfooding:** **TFN**
- **Client productions (Paid):** **10DLC per brand** (no shared numbers)

**Submission target (placeholder)**
- Provider: **Twilio** (can change later)
- Artifacts we submit: JSON + PDF summary from wizard output
- Storage: `s3://{bucket}/compliance/{tenantId}/{yyyy-mm-dd}/...`

**Acceptance for this doc**
- Channel choice is recorded exactly as above.
- Blocking rule present: no sends until **Approved**.
- Number assignment post-approval only.

