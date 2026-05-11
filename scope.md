# AWS Financial Services Landing Zone Scope

## Purpose

Define the minimum build scope for a greenfield AWS landing zone that can host regulated financial services workloads with strong governance, security, resilience, and auditability. This scope assumes a multi-account operating model, infrastructure as code, centralized security services, and control alignment to common financial sector expectations such as PCI DSS, ISO 27001, SOC 2, FCA/PRA-style operational resilience principles, and internal risk and audit requirements.

## Architectural Assumptions

- The landing zone will support production, non-production, shared services, and security workloads in separate AWS accounts.
- The environment will be built using AWS Organizations, AWS Control Tower or equivalent guardrails, and policy-as-code.
- All infrastructure will be deployed through approved CI/CD pipelines with segregation of duties and auditable approvals.
- Identity will be federated from an enterprise identity provider.
- Critical controls must be centrally enforceable, monitored, and evidenced.
- The design must support future multi-region resilience for critical business services.
- Cloud managed services are assumed to be used till clarification from the stakeholder on the need to incorproate external tools.

## Scope Summary

The landing zone build must deliver the following foundational capabilities:

1. AWS Organizations and account governance
2. Network and connectivity foundation
3. Federation, identity, and IAM controls
4. Security overlay and detective or preventive controls
5. Operations, observability, resilience, and service management
6. CI/CD, infrastructure delivery, and change governance

## RACI Legend

| Actor | Meaning |
|---|---|
| AWS | AWS implementation team |
| C - Governance | C - Governance |
| C - Security | C - Security |
| C - Compliance | C - Compliance |
| C - PM | C - Project Management & Governance |
| C - Platform Operations | C - Platform Engineering and Operations team |
> OW May represent C - roles during the project, depending on delivery phase and stakeholder assignment 

## 1. AWS Organizations

### Key Outputs

- Organization design and OU hierarchy
- Account baseline and account creation workflow
- SCP catalog and exception process
- Tagging standard, tag policy design, and enforcement controls
- Billing model and cost allocation design

### Required Build

| # | Task | R | A | C | I | Comments |
|---|------|---|---|---|---|----------|
| 1 | Discovery, Workshop and finalize design with stakeholders | AWS | C - PM | C - Governance | C - Platform Operations / C - Security / C - Compliance | |
| 2 | Establish an AWS Organization with all features enabled | AWS | C - Governance | C - Platform Operations | C - PM  |
| 3 | Define and deploy organizational unit structure (Security, Infrastructure/Shared Services, Development, Test/UAT, Production) | AWS | C - Governance | C - Security | C - PM | |
| 4 | Implement account vending and lifecycle management for new accounts | AWS | C - Platform Operations | C - Governance | C - PM | |
| 5 | Define mandatory tagging standards for owner, application, environment, data classification, cost center, and compliance domain |AWS | C - Governance | C - Compliance | C - PM | |
| 6 | Implement tagging enforcement controls across all accounts and resource types | AWS | C - Platform Operations | C - Governance | C - PM| |
| 7 | Configure centralized billing, cost allocation tags, budgets, and chargeback or showback reporting | AWS | C - Governance | C - Compliance | C - PM | |
| 8 | Apply service control policies to restrict non-approved regions, disallow destructive security changes, and enforce baseline controls | AWS | C - Security | C - Compliance | C - PM | |


## 2. Networks

### Key Outputs

- Network reference architecture
- IP address management strategy
- Connectivity model for internet, on-premises, and third parties
- Logging and inspection design

### Required Build

| # | Task | R | A | C | I | Comments |
|---|------|---|---|---|---|----------|
| 1 | Discovery, Workshop and finalize design with stakeholders | AWS | C - PM | C - Platform Operations / C - Security / C - Compliance | C - Governance | |
| 2 | Design a multi-account network topology with clear separation between internet-facing, private application, management, and security traffic | AWS | C - Platform Operations | C - Security / C- Compliance  |  C - PM | |
| 3 | Create dedicated networking patterns for shared ingress or egress, inspection, and hybrid connectivity | AWS | C - Platform Operations | C - Security / C- Compliance  |  C - PM | |
| 4 | Build VPC standards including CIDR strategy, subnet tiers, route table standards, NACL guidance, and security group patterns | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 5 | Implement centralized DNS using Route 53 public and private hosted zones with resolver controls | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 6 | Provide secure north-south and east-west traffic controls using AWS Network Firewall, Gateway Load Balancer-based inspection, or equivalent | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 7 | Define and implement firewall rules and network inspection coverage for phase 1 | AWS | C - Security | C - Platform Operations | C - PM | |
| 8 | Implement centralized egress controls, NAT strategy, and outbound filtering | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 9 | Implement outbound destination allowlisting controls including FQDN-based allowlisting where required by policy | AWS | C - Security | C - Platform Operations | C -PM | |
| 10 | Implement connectivity for on-premises if applicable and partner networks using Direct Connect, VPN, Transit Gateway, or Cloud WAN | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 11 | Enable VPC Flow Logs, Route 53 query logging, and network telemetry forwarding to central monitoring and security accounts | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 12 | Ensure isolation of production and non-production connectivity with controlled blast radius | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 13 | Reserve and manage address space and routing design for future inter and intra region deployment | AWS | C - Platform Operations | C - Governance | C - PM | |

## 3. Federation and IAM

### Key Outputs

- Identity federation design
- Access control matrix and privileged access model
- Break-glass procedure
- IAM baseline and access review process

### Required Build

| # | Task | R | A | C | I | Comments |
|---|------|---|---|---|---|----------|
| 1 | Discovery, Workshop and finalize design with stakeholders | AWS | C - PM | C - Security / C - Compliance / C - Platform Operations | C - Governance | |
| 2 | Integrate AWS access with the enterprise identity provider using AWS IAM Identity Center or an approved federation pattern | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 3 | Define role-based access control aligned to job functions (platform admin, security analyst, network engineer, developer, operator, auditor, read-only support) | AWS | C - Security | C - Platform Operations | C - PM | |
| 4 | Enforce multi-factor authentication for all human access | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 5 | Define Separate privileged access from standard user access and implement just-in-time or time-bound elevated access where possible | AWS | C - Security | C - Platform Operations | C - PM | |
| 6 | Establish break-glass access with strong approval, vaulting, monitoring, and periodic testing | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 7 | Define permission set standards, least-privilege role patterns, and cross-account access rules | AWS | C - Security | C - Platform Operations | C - PM | |
| 8 | Implement service roles and workload roles with short-lived credentials and no long-lived static access keys | AWS | C - Platform Operations | C - Security | C - PM | |
| 9 | Apply IAM guardrails for root account protection, key rotation, role trust policy restrictions, and permissions boundary usage for delegated builders | AWS | C - Platform Operations | C- Security / C - Compliance | C - PM | |
| 10 | Enable centralized access logging and evidence for user sign-in, role assumption, and privileged activities | AWS | C - Platform Operations |C - Security / C - Compliance | C - PM | |	

## 4. Security Overlay

### Key Outputs

- Security services architecture
- Control baseline mapped to policy and compliance objectives
- Key management and secrets management design
- Log bunkering and protected retention design
- Security monitoring and incident response model
- SOC provider integration model and handoff procedures

### Required Build

| # | Task | R | A | C | I | Comments |
|---|------|---|---|---|---|----------|
| 1 | Discovery, Workshop and finalize design with stakeholders | AWS | C - PM | C - Security / C - Compliance / C - Platform Operations | C - Governance | |
| 2 | Centralize security tooling in dedicated security accounts | AWS | C - Platform Operations | C - Security | C - PM | |
| 3 | Enable organization-wide CloudTrail with immutable retention into a log archive account | AWS | C - Platform Operations | C - Security | C - PM | |
| 4 | Implement a log-bunkering capability with isolated access, immutable storage controls, and protected retention for audit and forensic logs | AWS | C - Platform Operations | C - Security | C - PM | |
| 5 | Enable AWS Config organization aggregators and required conformance packs | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 6 | Enable GuardDuty, Security Hub, and IAM Access Analyzer across all in-scope accounts | AWS | C - Platform Operations | C - Security | C - PM | |
| 7 | Enable Inspector across all in-scope accounts for vulnerability assessment coverage | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 8 | Enable Detective in the security account once the SOC provider is active | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 9 | Define KMS key management architecture including customer managed keys, separation of duties, rotation, and key usage logging | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 10 | Enforce encryption at rest and in transit for all supported services | AWS | C - Platform Operations | C- Security / C - Compliance | C - PM | |
| 11 | Implement secrets management standards using AWS Secrets Manager or Parameter Store with rotation where applicable | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 12 | Build a vulnerability management process for AMIs, containers, Lambda dependencies, and operating systems | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 13 | Define baseline controls for WAF, certificate management, and edge protection for internet-exposed services | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 14 | Implement centralized log ingestion into a SIEM or security data lake | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 15 | Forward security and audit logs from the log bunker to approved downstream analytics platforms without making the source log store mutable | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 16 | Pipe security logs, detections, and operational security events to the appointed SOC provider using approved integration patterns | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 17 | Include secure integration for the SOC provider covering log transport, event normalization, alert enrichment, retention expectations, and escalation paths | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 18 | Create alert routing, incident triage, and response playbooks for high-severity findings | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 19 | Define evidence collection for audit, control attestations, and policy exceptions | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 20 | Include data classification, data residency, retention, and tokenization or masking requirements where needed for sensitive data | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |

## 5. Operations

### Key Outputs

- Monitoring and alerting baseline
- Systems management and automation design
- Backup and disaster recovery design
- Observability and telemetry architecture

### Required Build

| # | Task | R | A | C | I | Comments |
|---|------|---|---|---|---|----------|
| 1 | Discovery, Workshop and finalize design with stakeholders | AWS | C - PM | C - Platform Operations | C - Governance | |
| 2 | Implement a centralized observability stack for logs, metrics, traces, dashboards, and alarms | AWS | C - Platform Operations | C - Governance | C - PM | |
| 3 | Establish platform operations tooling for patching orchestration, automation, systems management, and configuration enforcement | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 4 | Implement backup services and policy-based backup configuration using AWS Backup and service-native capabilities | AWS | C - Platform Operations | C - Governance | C - PM | |
| 5 | Implement disaster recovery capabilities for critical shared services including cross-AZ and future cross-region recovery patterns | AWS | C - Platform Operations | C - Security / C - Compliance / C - Governance | C - PM | |
| 6 | Build service health monitoring, synthetic checks, and dependency visibility for critical platform components | AWS | C - Platform Operations | C - Compliance / C - Governance | C - PM | |
| 7 | Implement centralized log retention, archival, and search capabilities for platform telemetry | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 8 | Implement certificate, secret, and key expiry monitoring with automated alerting | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |
| 9 | Implement cost monitoring, anomaly detection, and capacity telemetry for shared platform services | AWS | C - Platform Operations | C - Governance | C - PM | |
| 10 | Configure backup policies and AI services opt-out or usage restrictions | AWS | C - Platform Operations | C - Compliance / C - Governance | C - PM | |
| 11 | Enable a central audit account and a central log archive account | AWS | C - Platform Operations | C - Security / C - Compliance | C - PM | |

## 6. CI/CD

### Key Outputs

- CI/CD reference architecture
- IaC standards and repository structure
- Pipeline control framework and approval model
- Deployment audit and rollback procedure

### Required Build

| # | Task | R | A | C | I | Comments |
|---|------|---|---|---|---|----------|
| 1 | Discovery, Workshop and finalize design with stakeholders | AWS | C - PM | C - Platform Operations / C - Securit| C - Governance | 
| 2 | Build a secure CI/CD platform for landing zone infrastructure and shared platform services | AWS | C - Platform Operations | C - Security | C - PM | |
| 3 | Use infrastructure as code for all foundational components (preferably Terraform, to be confirmed with stakeholders) | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 4 | Enforce branch protection, peer review, signed commits or equivalent assurance, and approval gates for production changes | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 5 | Separate build, deploy, and approval responsibilities to maintain segregation of duties | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 6 | Implement automated validation including linting, security scanning, policy compliance checks, unit tests, and drift detection | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 7 | Integrate secrets handling into pipelines without exposing credentials in code or logs | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 8 | Use artifact repositories with provenance, retention, and access controls | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 9 | Implement environment promotion controls with evidence capture for approvals and deployment history | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 10 | Define rollback procedures for failed infrastructure changes | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |
| 11 | Ensure pipelines can deploy to multiple accounts through tightly scoped cross-account roles | AWS | C - Platform Operations | C - Security / C- Compliance | C - PM | |

## Cross-Cutting Compliance Requirements

The landing zone must additionally provide the following cross-cutting controls:

- Audit-ready logging with retention aligned to legal, regulatory, and internal policy requirements
- Protected, immutable audit-log retention in a segregated log archive or log bunker account
- Control evidence generation for internal audit, external audit, and regulator review
- Policy exception workflow with expiry, approval, and compensating controls
- Data protection standards for encryption, classification, retention, and secure deletion
- Segregation of duties across engineering, operations, and security functions
- Resilience testing, backup testing, and failover rehearsal requirements
- Third-party connectivity controls and due diligence requirements
- Secure baseline documentation, architecture decision records, and operational procedures

## Deliverables

- Landing zone target architecture
- Organization and account design
- Network and connectivity design
- Identity and access model
- Security control architecture and logging design
- Operations and resilience model
- CI/CD and infrastructure delivery model
- Guardrail catalog and policy set
- Implementation roadmap with phases, dependencies, and control gates


## Open Points to Confirm

| # | Task | Responsible | Comments |
|---|------|-------------|----------|
| 1 | Confirm regulatory scope in focus (for example PCI DSS, ISO 27001, SOC 2,, or regional banking regulations) | C - Compliance |
| 2 | Confirm required AWS regions and any data residency constraints | C - Governance |
| 3 | Confirm enterprise identity provider and privileged access management platform | C - Platform and Operations |
| 4 | Confirm appointed SOC provider, SIEM ownership model, and required event ingestion interfaces | C - Security |
| 5 | Confirm RTO and RPO targets for critical business services | C - Governance |
| 6 | Cloud adoption risk register | C - Platform and Operations / C - Security / C - Compliance |
| 7 | Cloud operating model | Client Senior stake holders |
| 8 | Confirm preferred IaC and CI/CD toolchain | C - Platform and Operations |

*Note: These open points must be confirmed as part of the discovery and workshop sessions.*

## Scope that should be covered in separate initiatives or future phases

| # | Task | Work stream | Responsible | Comments |
|---|------|------------|-------------|----------|
| 1 | Define data loss prevention implementation pattern for cloud-native and third-party egress channels | Data and AI  | C - Security / C - Platform and Operations | |
| 2 | Detail ransomware-specific technical controls, including immutable backup controls, malware detection posture, and recovery hardening patterns | Security Overlay | C - Security | Address if this is an item in the risk register |
| 3 | Define cloud provider service event handling controls, including impact assessment automation and technical failover decision triggers | Operations | C - Platform and Operations | |
| 4 | Expand cost optimization implementation details for Savings Plans governance, storage tiering controls, and pricing-model guardrails | Operations | C - Governance | |
| 5 | Determine whether sustainability and carbon-aware technical controls are required by regulatory or internal policy commitments | Governance | C - Governance | |
| 6 | Define AI-specific security and performance controls once AI workloads are identified as part of the relevant delivery stream | Data and AI | C - Security / C - Platform and Operations | |
