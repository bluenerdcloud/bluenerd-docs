# 🧩 Epic: Google Ads Lead Capture Integration

This epic enables capture of leads from Google Ads Lead Form Extensions and custom site forms into the Blue-nerd sales app backend.

## 🎯 Objective
- Accept POST payloads from Google Ads webhook
- Store validated leads in RDS
- Support lead capture from site form
- Link all leads to sales workflow

---

## 🧩 Sub-Stories by Repo

### 🔧 bluenerd-sales-api
- [ ] #105 Lambda: Accept HTTPS leads from Google Ads → store in RDS
- [ ] #106 Validate and log webhook requests from Google Ads
- [ ] #107 Add test endpoint for webhook verification
- [ ] #108 Add unit tests for Google Ads lead handler

### 🏗 bluenerd-infra
- [ ] #77 Create API Gateway POST endpoint `/google-ads/lead`
- [ ] #78 IAM role + policy for Lambda to access RDS
- [ ] #79 Create table `google_ads_leads` in Terraform
- [ ] #80 Secure endpoint with auth header (shared secret)

### 🎨 bluenerd-sales-app
- [ ] #31 Frontend form: capture leads from ad click
- [ ] #32 Submit to backend endpoint
- [ ] #33 Confirmation UI
- [ ] #34 Handle errors gracefully

---

## 🧪 Test Plan
- [ ] Google webhook verified (200 OK response)
- [ ] Manual form submission appears in RDS
- [ ] Sales UI (future) can display lead data
- [ ] Auth header blocks unauthorized requests

---

## 🔗 Labels and Milestone
All stories should have:
- Label: `epic:google-ads`
- Milestone: `MVP2`
