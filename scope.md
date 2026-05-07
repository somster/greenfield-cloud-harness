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

## 1. AWS Organizations

### Required Build

- Establish an AWS Organization with all features enabled.
- Define an organizational unit structure for at minimum:
	- Security
	- Infrastructure or Shared Services
	- Development
	- Test or UAT
	- Production
- Implement account vending and lifecycle management for new accounts.
- Configure centralized billing, cost allocation tags, budgets, and chargeback or showback reporting.
- Define mandatory tagging standards for owner, application, environment, data classification, cost center, and compliance domain.
- Implement tagging enforcement controls to ensure compliance with the tagging standard across all accounts and resource types.
- Apply service control policies to restrict non-approved regions, disallow destructive security changes, and enforce baseline controls.
- Configure backup policies and AI services opt-out or usage restrictions if required by policy.
- Enable a central audit account and a central log archive account.

### Key Outputs

- Organization design and OU hierarchy
- Account baseline and account creation workflow
- SCP catalog and exception process
- Tagging standard, tag policy design, and enforcement controls
- Billing model and cost allocation design

## 2. Networks

### Required Build

- Design a multi-account network topology with clear separation between internet-facing, private application, management, and security traffic.
- Create dedicated networking patterns for shared ingress or egress, inspection, and hybrid connectivity.
- Build VPC standards including CIDR strategy, subnet tiers, route table standards, NACL guidance, and security group patterns.
- Implement centralized DNS using Route 53 public and private hosted zones with resolver controls.
- Provide secure north-south and east-west traffic controls using AWS Network Firewall, Gateway Load Balancer-based inspection, or equivalent inspection services where required.
- Implement centralized egress controls, NAT strategy, and outbound filtering.
- Implement outbound destination allowlisting controls, including FQDN-based allowlisting where required by policy.
- Design connectivity for on-premises and partner networks using Direct Connect, VPN, Transit Gateway, or Cloud WAN as appropriate.
- Enable VPC Flow Logs, Route 53 query logging, and network telemetry forwarding to central monitoring and security accounts.
- Define separate patterns for production and non-production connectivity with controlled blast radius.
- Reserve address space and routing design for future multi-region deployment.

### Key Outputs

- Network reference architecture
- IP address management strategy
- Connectivity model for internet, on-premises, and third parties
- Logging and inspection design

## 3. Federation and IAM

### Required Build

- Integrate AWS access with the enterprise identity provider using AWS IAM Identity Center or an approved federation pattern.
- Define role-based access control aligned to job functions such as platform admin, security analyst, network engineer, developer, operator, auditor, and read-only support.
- Enforce multi-factor authentication for all human access.
- Separate privileged access from standard user access and implement just-in-time or time-bound elevated access where possible.
- Establish break-glass access with strong approval, vaulting, monitoring, and periodic testing.
- Define permission set standards, least-privilege role patterns, and cross-account access rules.
- Implement service roles and workload roles with short-lived credentials and no long-lived static access keys.
- Apply IAM guardrails for root account protection, key rotation, role trust policy restrictions, and permissions boundary usage for delegated builders.
- Enable centralized access logging and evidence for user sign-in, role assumption, and privileged activities.

### Key Outputs

- Identity federation design
- Access control matrix and privileged access model
- Break-glass procedure
- IAM baseline and access review process

## 4. Security Overlay

### Required Build

- Centralize security tooling in dedicated security accounts.
- Enable organization-wide CloudTrail with immutable retention into a log archive account.
- Implement a log-bunkering capability using a dedicated log archive pattern with isolated access, immutable storage controls, and protected retention for audit and forensic logs.
- Enable AWS Config organization aggregators and required conformance packs.
- Enable GuardDuty, Security Hub, and IAM Access Analyzer across all in-scope accounts as mandatory baseline detective controls.
- Enable Inspector across all in-scope accounts to provide vulnerability assessment coverage once workloads begin onboarding.
- Enable Detective in the security account once the SOC provider is active and requires investigation and threat-hunting capability; defer until then to avoid unnecessary cost.
- Define KMS key management architecture including customer managed keys, separation of duties, rotation, and key usage logging.
- Enforce encryption at rest and in transit for all supported services.
- Implement secrets management standards using AWS Secrets Manager or Parameter Store with rotation where applicable.
- Build a vulnerability management process for AMIs, containers, Lambda dependencies, and operating systems.
- Define baseline controls for WAF, certificate management, and edge protection for internet-exposed services.
- Implement centralized log ingestion into a SIEM or security data lake.
- Forward security and audit logs from the log bunker to approved downstream analytics platforms without making the source log store mutable.
- Pipe security logs, detections, and relevant operational security events to the appointed SOC provider or managed detection and response service using approved integration patterns.
- Include secure integration for the SOC provider covering log transport, event normalization, alert enrichment, retention expectations, and escalation paths.
- Create alert routing, incident triage, and response playbooks for high-severity findings.
- Define evidence collection for audit, control attestations, and policy exceptions.
- Include data classification, data residency, retention, and tokenization or masking requirements where needed for sensitive data.

### Key Outputs

- Security services architecture
- Control baseline mapped to policy and compliance objectives
- Key management and secrets management design
- Log bunkering and protected retention design
- Security monitoring and incident response model
- SOC provider integration model and handoff procedures

## 5. Operations

### Required Build

- Implement a centralized observability stack for logs, metrics, traces, dashboards, and alarms.
- Establish platform operations tooling for patching orchestration, automation, systems management, and configuration enforcement.
- Implement backup services and policy-based backup configuration using AWS Backup and service-native capabilities.
- Implement disaster recovery capabilities for critical shared services, including cross-AZ and future cross-region recovery patterns where required.
- Build service health monitoring, synthetic checks, and dependency visibility for critical platform components.
- Implement centralized log retention, archival, and search capabilities for platform telemetry.
- Implement certificate, secret, and key expiry monitoring with automated alerting.
- Implement cost monitoring, anomaly detection, and capacity telemetry for shared platform services.

### Key Outputs

- Monitoring and alerting baseline
- Systems management and automation design
- Backup and disaster recovery design
- Observability and telemetry architecture

## 6. CI/CD

### Required Build

- Build a secure CI/CD platform for landing zone infrastructure and shared platform services.
- Use infrastructure as code for all foundational components, preferably Terraform but to be clarified with stakeholders.
- Enforce branch protection, peer review, signed commits or equivalent assurance, and approval gates for production changes.
- Separate build, deploy, and approval responsibilities to maintain segregation of duties.
- Implement automated validation including linting, security scanning, policy compliance checks, unit tests, and drift detection.
- Integrate secrets handling into pipelines without exposing credentials in code or logs.
- Use artifact repositories with provenance, retention, and access controls.
- Implement environment promotion controls with evidence capture for approvals and deployment history.
- Define rollback procedures for failed infrastructure changes.
- Ensure pipelines can deploy to multiple accounts through tightly scoped cross-account roles.

### Key Outputs

- CI/CD reference architecture
- IaC standards and repository structure
- Pipeline control framework and approval model
- Deployment audit and rollback procedure

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

## Recommended Delivery Phases

1. Foundation governance and account structure
2. Core network and shared connectivity services
3. Identity federation and privileged access controls
4. Centralized logging, security services, and detective controls
5. Operations tooling, backup, and resilience capabilities
6. CI/CD platform, policy-as-code, and automated compliance checks
7. Pilot onboarding of first regulated workload

## Open Points to Confirm

- Regulatory scope in focus, for example PCI DSS, ISO 27001, SOC 2, FCA, PRA, MAS, or regional banking regulations
- Required AWS regions and any data residency constraints
- Enterprise identity provider and privileged access management platform
- Appointed SOC provider, SIEM ownership model, and required event ingestion interfaces
- RTO and RPO targets for critical business services
- Preferred IaC and CI/CD toolchain

## Gaps for Subsequent Iteration

- Data loss prevention implementation pattern to be defined for cloud-native and third-party egress channels.
- Ransomware-specific technical controls to be detailed, including immutable backup controls, malware detection posture, and recovery hardening patterns.
- Cloud provider service event handling controls to be detailed, including impact assessment automation and technical failover decision triggers.
- Cost optimization implementation details to be expanded for Savings Plans governance, storage tiering controls, and pricing-model guardrails.
- Sustainability and carbon-aware technical controls are deferred for a later iteration unless required by regulatory or internal policy commitments.
- AI-specific security and performance controls are pending identification of AI workloads and are expected to be handled as part of the delivery of that stream of work.
