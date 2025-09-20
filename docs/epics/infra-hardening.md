# Epic: Infra Hardening

## Goal
Ensure Blue Nerd Cloud infrastructure is **repeatable, secure, and production-ready**, moving beyond the bare-bones setup delivered in MVP1.  
This epic strengthens both **infrastructure** and **applications** by introducing documentation, automation, and security best practices.

---

## Problem Statement
MVP1 delivered core infrastructure manually. While functional, it relies on developer knowledge and ad-hoc steps.  
Without proper hardening:
- New contributors struggle to reproduce environments.  
- Secrets and credentials may be handled inconsistently.  
- Infrastructure changes risk drift and misconfiguration.  
- Scaling into later MVPs or production is fragile.  

---

## Business Impact
- Reduces onboarding time for new developers (“guest chefs”).  
- Improves security posture (secret management, IAM least privilege).  
- Makes infra **repeatable across DEV, STAGE, PROD**.  
- Provides a foundation for **MVP2 and beyond** without rework.  
- Builds trust that infra is safe for customer data and production workloads.  

---

## Scope & Stories

### Documentation
- `infra-readme`: Clear Terraform lifecycle (`init`, `plan`, `apply`, `destroy`).  
- Document required secrets, AWS account assumptions, backend state config.  

### Security & Secrets
- Centralize DB and API credentials in **SSM/Secrets Manager**.  
- Secure API Gateway with **auth headers**, WAF, throttling, logging.  
- IAM policies for **least privilege**.  

### Automation
- Bastion host automation — provision per-env, reuse PEM keys.  
- CI/CD pipelines — GitHub Actions to deploy infra automatically.  
- Terraform state management (remote backend).  

### Monitoring & Observability
- CloudWatch dashboards for Lambda, API Gateway, and RDS.  
- Error alerts and log aggregation.  

---

## Acceptance Criteria
- [ ] Any contributor can provision and destroy DEV infra using documented steps.  
- [ ] All sensitive values (DB creds, API secrets) stored in AWS SSM or Secrets Manager.  
- [ ] Infra deployable via CI/CD pipeline, not just manual CLI.  
- [ ] API endpoints secured and observable.  
- [ ] Monitoring and alerts in place for key components.  

---

## Dependencies
- **MVP1 – Core Infra + Manual Deployment** (baseline infra must exist).  
- **MVP2 – Google Ads Lead Capture** depends on hardened infra to ensure security and repeatability.  
