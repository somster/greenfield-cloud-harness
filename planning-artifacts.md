# Non-Technical Planning Artifacts

## Purpose

These artifacts must be produced **before or in parallel** with the technical landing zone build phases. They represent the planning decisions, policy approvals, and governance structures that steer implementation. Sourced from the AWS Well-Architected Financial Services Industry Lens and cross-referenced against the build scope.

Delivery of the landing zone without these artifacts risks building controls that cannot be mapped to a risk, applying retention policies that Legal has not approved, and running DR tests against recovery objectives that the business has not committed to.

---

## 1. Cloud Risk Register

**FSI Lens reference:** FSIOPS01 — Three Lines of Defense model, RACI, risk appetite

**Needed before:** Phase 1 – AWS Organizations and account governance

| Risk ID | Risk Description | Likelihood | Impact | Risk Owner (1LOD) | Control Owner (2LOD) | Control Type | Residual Risk | Status |
|---------|-----------------|------------|--------|-------------------|---------------------|--------------|---------------|--------|
| CR-001 | Unauthorised access to production accounts | Medium | Critical | Platform Engineering | Cloud Security | Preventive – SCPs, IAM | | Open |
| CR-002 | Loss of immutable audit logs | Low | Critical | SecOps | Compliance | Detective + Preventive – S3 Object Lock | | Open |
| CR-003 | Uncontrolled lateral movement across accounts | Medium | High | Network Engineering | Cloud Security | Preventive – Network Firewall, segmentation | | Open |
| CR-004 | Third-party SOC provider data exposure | Low | High | CISO | 3rd Party Risk | Contractual + Technical | | Open |
| CR-005 | CI/CD pipeline compromise leading to production change | Medium | Critical | DevOps | Cloud Security | Preventive – branch protection, SoD | | Open |
| CR-006 | Regulatory non-compliance in log retention | Low | Critical | Compliance | Legal | Detective – retention policy enforcement | | Open |
| CR-007 | Recovery objectives not met for critical service | Medium | Critical | Platform Engineering | Resilience | Preventive + Tested DR | | Open |
| CR-008 | Root account compromise | Low | Critical | Cloud Security | CISO | Preventive – root account lock, MFA, alerting | | Open |
| CR-009 | Misconfigured SCP blocks legitimate workload access | Medium | High | Platform Engineering | Cloud Security | Detective – Config rules, change review | | Open |
| CR-010 | Encryption key deletion causing data loss | Low | Critical | Cloud Security | Compliance | Preventive – key deletion window, alarm | | Open |

---

## 2. Regulatory Obligations Register

**FSI Lens reference:** FSIOPS03, FSISEC02 — Applicable regulations must be identified before control design

**Needed before:** Phase 3 – Federation, Identity, and IAM controls

> **Action required:** Confirm applicable regulations with Compliance and Legal. Control design for encryption, log retention, data residency, and incident notification timelines diverges materially based on the regulatory scope.

| Regulation | Applicable | Primary Obligation | Control Area | Evidence Required | Review Cadence |
|------------|------------|-------------------|--------------|-------------------|----------------|
| PCI DSS v4 | TBC | Cardholder data protection, network segmentation | Security, Network | QSA assessment, log evidence | Annual + quarterly scans |
| FCA / PRA Operational Resilience | TBC | Impact tolerance for important business services | Resilience, Operations | Scenario testing evidence, RTO/RPO proof | Annual |
| ISO 27001 | TBC | Information security management system | All pillars | ISMS documentation, audit trail | Annual certification |
| SOC 2 Type II | TBC | Trust service criteria – security and availability | Security, Availability | Continuous monitoring evidence | Annual |
| GDPR / UK GDPR | TBC | Data subject rights, residency, retention | Data protection, IAM | DPIA, processing records | Ongoing |
| MAS TRM (Singapore, if applicable) | TBC | Technology risk management framework | Security, Operations | Risk assessment reports | Annual |
| DORA (EU, if applicable) | TBC | Digital operational resilience | Resilience, Incident response | ICT risk register, testing evidence | Annual |

---

## 3. Workload Criticality and Recovery Tier Register

**FSI Lens reference:** FSIOPS01-BP03, FSIREL03 — Risk appetite and recovery objectives are set by the business; architecture follows

**Needed before:** Phase 2 – Network and connectivity foundation

> Architecture for network segmentation, multi-AZ patterns, and DR strategy cannot be finalised without agreed RTO and RPO targets per workload tier.

| Workload / Service | Business Function | Criticality Tier | Availability SLO | Max RTO | Max RPO | DR Strategy | Data Classification | Regulatory Flag |
|-------------------|------------------|-----------------|-----------------|---------|---------|-------------|--------------------|----|
| Core Banking API | Payment processing | Platinum – Tier 1 | 99.99% | 15 min | 30 sec | Active-active multi-AZ | Restricted | PCI, FCA |
| Fraud Detection Engine | Risk management | Gold – Tier 2 | 99.9% | 2 hrs | 2 hrs | Warm standby | Confidential | FCA |
| Customer Identity / Authentication | Authentication | Platinum – Tier 1 | 99.99% | 15 min | 30 sec | Active-active | Restricted | GDPR |
| DevOps Toolchain (CI/CD) | Engineering delivery | Gold – Tier 2 | 99.9% | 4 hrs | 1 hr | Pilot light | Internal | |
| Audit Log Archive | Regulatory evidence | Platinum – Tier 1 | 99.99% | N/A – write-only | 0 | Immutable, cross-region | Restricted | All |
| Shared Network Services | Connectivity | Platinum – Tier 1 | 99.99% | 15 min | N/A | Redundant Direct Connect + VPN | Internal | FCA |
| Monitoring and Observability | Operations | Gold – Tier 2 | 99.9% | 2 hrs | 1 hr | Warm standby | Internal | |

**Resilience tier reference (FSI Lens Table 1 and 2):**

| Tier | Availability SLO | Max RTO | Max RPO | Criteria |
|------|-----------------|---------|---------|---------|
| Platinum – Tier 1 | 99.99% | 15 minutes | 30 seconds | Mission-critical workloads |
| Gold – Tier 2 | 99.90% | 15 minutes – 8 hours | 2 hours | Important, not mission-critical |
| Silver – Tier 3 | 98% | 6 hours – a few days | 24 hours | Non-critical workloads |

---

## 4. Data Classification Policy

**FSI Lens reference:** FSISEC09, FSISEC10, FSIREL10 — Retention, encryption, storage tiers, and backup policies all flow from data classification

**Needed before:** Phase 4 – Security overlay and detective controls

> WORM retention policies applied via S3 Object Lock cannot be shortened after the fact. Legal and Compliance must approve retention periods before the log archive account is built.

| Classification | Definition | Examples | Encryption Required | Retention Period | Storage Tier | Access Restriction | DLP Controls |
|---------------|------------|---------|---------------------|-----------------|--------------|-------------------|---|
| Restricted | Regulated data or customer PII | Card data, KYC records, audit logs, biometrics | Customer-managed KMS key (CMK) | 7 years minimum | S3 + Glacier Deep Archive, WORM | Named roles only, MFA required | Yes |
| Confidential | Sensitive internal data | Risk models, pricing algorithms, internal reports | AWS-managed or CMK | 3–5 years | S3 Standard-IA | Role-based, least privilege | Yes |
| Internal | General business data | Architecture diagrams, runbooks, internal communications | AWS-managed | 2 years | S3 Standard | IAM policy | No |
| Public | Published externally | Marketing content, public API responses | In-transit only | As business requires | CloudFront / S3 | None | No |

---

## 5. Cloud Operating Model and Accountability RACI

**FSI Lens reference:** FSIOPS01-BP01 — Publishing a RACI reduces misunderstandings about which role owns each activity across the three lines of defence

**Needed before:** Phase 1 – AWS Organizations and account governance

| Capability | Platform Engineering | Cloud Security | Network Engineering | Application Teams | Compliance / 2LOD | Internal Audit / 3LOD |
|------------|---------------------|---------------|---------------------|------------------|-------------------|----------------------|
| Account vending and lifecycle | R | C | I | I | C | I |
| Organisational unit design | R | A | I | I | C | I |
| SCP authoring and enforcement | R | A | I | I | C | I |
| IAM permission set design | R | A | C | C | C | I |
| Security baseline deployment (GuardDuty, Config, Security Hub) | R | A | I | I | C | I |
| Log archive management | R | A | I | I | C | A |
| Network segmentation design | R | C | A | C | C | I |
| Change approval for production | C | C | C | R | A | I |
| Incident response execution | R | A | I | C | C | I |
| Break-glass access activation | C | A | I | I | C | I |
| DR test execution | R | A | C | C | C | I |
| Evidence collection for audit | C | C | I | I | R | A |
| Tagging compliance enforcement | R | A | I | C | C | I |
| Cost anomaly review | R | I | I | C | C | I |

---

## 6. Key Management and Secrets Policy

**FSI Lens reference:** FSISEC09 — Customer-managed key decisions, rotation schedules, and separation of duties must be documented before KMS is deployed

**Needed before:** Phase 4 – Security overlay

| Key Type | AWS Service | Managed By | Rotation Period | Separation of Duties | Cross-Region Replication | Evidence Required |
|----------|------------|------------|----------------|---------------------|-------------------------|------------------|
| Log archive encryption | S3, CloudTrail | CMK – Security team | Annual | Key admin role ≠ key user role | Yes – secondary region | CloudTrail key usage logs |
| Database encryption | RDS, DynamoDB | CMK – Platform team | Annual | Defined IAM permissions boundary | No (data replicated separately) | AWS Config managed rule |
| Secrets (API keys, credentials) | Secrets Manager | Automated rotation | 30–90 days | Application role access only | Yes if multi-region workload | Rotation audit log |
| TLS certificates | ACM | AWS-managed auto-renew | Automatic | N/A | Regional | Expiry alerting via CloudWatch |
| Code signing | ACM or CMK | Security team | Annual | Separate approval for signing authority | N/A | Signing event log |

---

## 7. Log Retention and Bunkering Policy

**FSI Lens reference:** FSISEC10-BP01, FSIREL10 — Retention periods must be approved by Legal and Compliance before the log archive account is built

**Needed before:** Phase 4 – Security overlay

| Log Type | Source | Retention Period | Storage Class | WORM Required | Access Permitted | Regulatory Driver |
|---------|--------|-----------------|---------------|---------------|-----------------|------------------|
| CloudTrail management events | All accounts | 7 years | S3 Glacier Deep Archive | Yes – S3 Object Lock | Security team read-only | FCA, ISO 27001, PCI DSS |
| CloudTrail data events (S3, Lambda) | Production accounts | 3 years | S3 Standard-IA → Glacier | Yes | SecOps read | PCI DSS |
| VPC Flow Logs | All VPCs | 2 years | S3 Standard-IA → Glacier | No | SecOps read | FCA, internal policy |
| Application logs | Workload accounts | 3 years | S3 Standard → IA | No | App team, SecOps | Internal policy |
| Security findings (GuardDuty, Security Hub) | Security account | 7 years | S3 Glacier | Yes | CISO, Compliance | Regulatory evidence |
| DNS query logs | Route 53 Resolver | 1 year | S3 Standard-IA | No | SecOps | Internal policy |
| Config history and snapshots | All accounts | 7 years | S3 Glacier Deep Archive | Yes | Compliance read-only | Audit evidence |
| Access and authentication logs | IAM Identity Center | 7 years | S3 Glacier | Yes | Compliance, Audit | FCA, GDPR |

---

## 8. Third-Party and Vendor Due Diligence Register

**FSI Lens reference:** Scope.md Open Points — SOC provider, SIEM ownership, and connectivity providers must be selected before integration can be designed

**Needed before:** Phase 2 (connectivity), Phase 4 (security)

| Vendor / Service | Category | Selection Status | Due Diligence Status | Data Shared | Contractual Controls Required | Integration Point in Build |
|-----------------|----------|-----------------|---------------------|-------------|-------------------------------|--------------------------|
| SOC Provider (MDR / MSSP) | Security operations | TBC | Not started | Security logs, findings, alerts | Data processing agreement, data residency clause, SLA | Phase 4 – Security overlay |
| SIEM Platform | Log analytics | TBC | Not started | All security telemetry | Data residency, retention obligations | Phase 4 – Security overlay |
| Direct Connect / Co-location Provider | Connectivity | TBC | Not started | Network traffic metadata | Physical security standards, SLA | Phase 2 – Networks |
| Privileged Access Management (PAM) tool | IAM | TBC | Not started | Privileged session recordings | Vaulting standards, integration security | Phase 3 – IAM |
| CI/CD toolchain (e.g. Terraform Cloud, GitHub) | Infrastructure delivery | TBC | Not started | IaC state, pipeline secrets | Secrets hygiene, audit log access | Phase 6 – CI/CD |
| Penetration testing provider | Security assurance | TBC | Not started | Architecture details | Scoping agreement, AWS pen test notification | Phase 4 onwards |

---

## 9. Incident Response and Regulatory Notification Framework

**FSI Lens reference:** FSISEC12, FSIOPS06 — Playbooks and regulatory notification timelines must be defined before the platform is operational

**Needed before:** Phase 7 – Pilot workload onboarding (but initiated in Phase 4)

| Severity | Definition | Internal Notification | Regulatory Notification | Evidence Required | Playbook Owner |
|----------|------------|----------------------|------------------------|------------------|---------------|
| P1 – Critical | Customer data breach, system unavailability exceeding RTO | Immediate – CISO, CTO | Within 72 hours (GDPR, FCA) | Full audit trail, incident timeline, RCA | CISO |
| P2 – High | Active security finding, significant partial degradation | Within 1 hour | Within 7 days if material impact | Incident log, containment actions | SecOps |
| P3 – Medium | Non-critical security finding, configuration drift | Within 24 hours | As required by regulation | AWS Config evidence, ticket record | Platform Engineering |
| P4 – Low | Informational finding, no customer impact | Next business day | None | Ticket record | Any team |

**Break-glass procedure requirements (Phase 3):**
- Named break-glass accounts with documented approval workflow
- Time-limited credentials with automatic expiry
- All sessions logged and alerted to CISO in real time
- Periodic testing schedule (minimum quarterly)
- Post-use review and re-vaulting procedure

---

## 10. Open Points Requiring Stakeholder Decisions

These items from scope.md must be resolved before the phases listed. Architectural decisions cannot be finalised without them.

| Open Point | Decision Required | Blocks | Decision Owner | Target Date |
|-----------|-----------------|--------|---------------|-------------|
| Regulatory scope (PCI DSS, FCA, ISO 27001, MAS, DORA) | Which regulations apply | All phases | Compliance / Legal | Before Phase 1 |
| AWS regions and data residency constraints | Primary and secondary region selection | Phase 2 – Networks | CTO + Compliance | Before Phase 2 |
| Enterprise identity provider | Which IdP federates to IAM Identity Center | Phase 3 – IAM | CISO + IT | Before Phase 3 |
| Privileged access management platform | Whether an external PAM tool is required | Phase 3 – IAM | CISO | Before Phase 3 |
| SOC provider and SIEM ownership | Who operates the SOC and owns the SIEM | Phase 4 – Security | CISO | Before Phase 4 |
| Required event ingestion interfaces | What formats / protocols the SOC accepts | Phase 4 – Security | CISO + SOC provider | Before Phase 4 |
| RTO and RPO targets for critical services | Tier assignment per workload | Phase 2 – Networks | Business leads | Before Phase 2 |
| Preferred IaC toolchain | Terraform vs CloudFormation vs CDK | Phase 6 – CI/CD | Platform Eng + CTO | Before Phase 1 |

---

## Planning Artifact Status Summary

| Artifact | Needed Before Phase | Owner | Status |
|---------|---------------------|-------|--------|
| Cloud Risk Register | Phase 1 | Cloud Security + 2LOD | Not started |
| Regulatory Obligations Register | Phase 3 | Compliance + Legal | Not started |
| Workload Criticality and RTO/RPO Tiers | Phase 2 | Business leads + Platform Eng | Not started |
| Data Classification Policy | Phase 4 | Data / Compliance + Legal | Not started |
| Cloud Operating Model RACI | Phase 1 | CTO / CISO | Not started |
| Key Management and Secrets Policy | Phase 4 | Cloud Security | Not started |
| Log Retention Schedule | Phase 4 | Legal + Compliance | Not started |
| Third-Party Due Diligence Register | Phase 2 and 4 | Procurement + 2LOD | Not started |
| Incident Response Playbooks | Phase 7 (initiated Phase 4) | SecOps / CISO | Not started |
| Break-glass Procedure | Phase 3 | Platform Eng + CISO | Not started |
| Open Points — Stakeholder Decisions | Phase 1 | CTO + Compliance | In progress – see scope.md |
