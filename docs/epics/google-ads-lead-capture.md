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
- [X] #105 Lambda: Accept HTTPS leads from Google Ads → store in RDS
- [X] #106 Validate and log webhook requests from Google Ads
- [X] #107 Add test endpoint for webhook verification
- [X] #108 Add unit tests for Google Ads lead handler

### 🏗 bluenerd-infra
- [X] #77 Create API Gateway POST endpoint `/google-ads/lead`
- [X] #78 IAM role + policy for Lambda to access RDS
- [X] #79 Create table `google_ads_leads` in Terraform
- [X] #80 Secure endpoint with auth header (shared secret)

### 🎨 bluenerd-sales-app
- [ ] #31 Frontend form: capture leads from ad click
- [X] #32 Submit to backend endpoint
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
