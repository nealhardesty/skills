# Application Threat Model Template

## 1. Application Summary

**Purpose:** Identify what the application is, who owns it, what is in scope, and the status of the threat model.

**Expectations:**

- Describe the application in plain language.
- Identify the business and technical owners.
- Define what this threat model does and does not cover.
- Capture review status and participants.

| Field | Value |
| --- | --- |
| Application Name |  |
| Version / Release |  |
| Environment(s) Covered | Dev / Test / Staging / Production |
| Business Owner |  |
| Technical Owner |  |
| Document Owner |  |
| Participants |  |
| Security Reviewer |  |
| Last Reviewed |  |
| Status | Draft / Reviewed / Approved |
| RFC Document |  |

**Application description:**  
Example: Customer Portal allows customers to view invoices, manage users, and submit support requests.

**Primary users:**  
Example: Customer users, customer administrators, and internal support users.

**In scope:**  
Example: Authentication, invoice viewing, user invitation, support ticket creation.

**Out of scope:**  
Example: Internal billing system, third-party payment processor.

**Assumptions:**  
Example: Users authenticate through the corporate identity provider. Production traffic is encrypted in transit.

## 2. Architecture, Data Flow, and Dependencies

**Purpose:** Show how the application works, how data moves through it, and which external systems it depends on.

**Expectations:**

- Include or link to at least one architecture or data flow diagram.
- Show users, application components, data stores, external systems, and trust boundaries.
- Identify dependencies that affect the application's security posture.

### Diagrams

| Diagram | Link / Location | Notes |
| --- | --- | --- |
| Context / Architecture Diagram |  | Shows major systems and integrations |
| Data Flow Diagram |  | Shows data movement and trust boundaries |

**Example:**

| Diagram | Link / Location | Notes |
| --- | --- | --- |
| Customer Portal DFD | `/docs/security/customer-portal-dfd.drawio` | Shows browser, API, identity provider, database, billing API, and support system |

### External Dependencies

| ID | Dependency | Owner | Description | Security Assumptions / Requirements |
| --- | --- | --- | --- | --- |
| EXT-001 |  |  |  |  |

**Example:**

| Dependency | Owner | Description | Security Assumptions / Requirements |
| --- | --- | --- | --- |
| Corporate Identity Provider | IAM Team | Provides SSO and MFA | OIDC tokens must be validated by the application |
| Billing API | Finance Systems | Provides invoice data | Access restricted to approved service accounts |

## 3. Assets, Data, and Trust Levels

**Purpose:** Identify what needs protection and who or what is allowed to access it.

**Expectations:**

- List sensitive data, credentials, critical system capabilities, and availability-sensitive workflows.
- Include human users and service identities.
- Capture confidentiality, integrity, and availability concerns where relevant.

### Assets and Data

| Asset | Type | Description | Security Concern | Classification |
| --- | --- | --- | --- | --- |
|  | Data / Credential / Capability / Availability |  | Confidentiality / Integrity / Availability | Public / Internal / Confidential / Restricted |

**Example:**

| Asset | Type | Description | Security Concern | Classification |
| --- | --- | --- | --- | --- |
| Customer invoice data | Data | Invoice amounts, dates, account identifiers | Confidentiality, integrity | Confidential |
| Admin user management | Capability | Ability to invite, disable, or modify users | Integrity, privilege abuse | Restricted |
| API access tokens | Credential | Tokens used to access application APIs | Confidentiality | Restricted |

### Trust Levels and Roles

| Role / Trust Level | Description | Example Permissions |
| --- | --- | --- |
|  |  |  |

**Example:**

| Role / Trust Level | Description | Example Permissions |
| --- | --- | --- |
| Anonymous User | User who has not authenticated | View login page |
| Authenticated Customer User | Customer user with valid login | View own account and tickets |
| Customer Admin | Customer user with administrative access | Invite users, manage account settings |
| Internal Support User | Employee supporting customers | View support-related customer records |
| Service Account | Non-human application identity | Read billing data from Billing API |

## 4. Entry Points, Exit Points, and Trust Boundaries

**Purpose:** Identify where data enters or leaves the application and where security decisions must be enforced.

**Expectations:**

- Include APIs, web forms, file uploads, webhooks, queues, admin interfaces, background jobs, and third-party integrations.
- Document authentication and authorization expectations.
- Identify where data crosses from one trust level to another.

| Type | Name | Description | Source / Destination | Required Trust Level | Related Assets |
| --- | --- | --- | --- | --- | --- |
| Entry |  |  |  |  |  |
| Exit |  |  |  |  |  |

**Example:**

| Type | Name | Description | Source / Destination | Required Trust Level | Related Assets |
| --- | --- | --- | --- | --- | --- |
| Entry | Login Endpoint | Accepts identity-provider callback | Browser / IdP | Anonymous User | API access tokens |
| Entry | Invite User API | Allows customer admins to invite users | Browser | Customer Admin | Admin user management |
| Entry | Support Ticket Form | Accepts customer support requests | Browser | Authenticated Customer User | Support ticket data |
| Exit | Invoice API Response | Returns invoice details to the UI | Browser | Authenticated Customer User | Customer invoice data |
| Exit | Support Ticket Sync | Sends ticket data to support platform | Support System API | Service Account | Support ticket data |
| Exit | Application Logs | Sends events and errors to logging platform | Centralized Logging | Service Account | Audit data, possible sensitive metadata |

## 5. Threats, Risks, and Mitigations

**Purpose:** Identify realistic threats, rate their risk, and document how they will be handled.

**Expectations:**

- Use STRIDE as the default threat prompt:
  - Spoofing
  - Tampering
  - Repudiation
  - Information Disclosure
  - Denial of Service
  - Elevation of Privilege
- Focus on application-specific threats, not generic checklist items.
- Include abuse/misuse scenarios directly in the threat description where relevant.
- Every High and Medium risk must have a mitigation, owner, or documented risk acceptance.
- Mitigations should be specific and testable.

| STRIDE Category | Threat / Abuse Scenario | Affected Asset(s) | Entry / Flow | Existing Controls | Risk | Mitigation / Requirement | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
|  |  |  |  |  | High / Medium / Low |  |  | Open / In Progress / Done / Accepted |

**Examples:**

| STRIDE Category | Threat / Abuse Scenario | Affected Asset(s) | Entry / Flow | Existing Controls | Risk | Mitigation / Requirement | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Spoofing | Attacker attempts to reuse or forge an authentication token to access another customer account | API access tokens, customer data | Login / API requests | OIDC token validation, token expiration | High | Validate issuer, audience, expiration, and signature on every API request | Platform Engineering | Done |
| Tampering | Customer modifies account ID in an API request to view another customer's invoices | Customer invoice data | Invoice API | Server-side authorization checks | High | Enforce tenant authorization server-side for every invoice lookup | App Team | In Progress |
| Information Disclosure | Sensitive invoice details are written to application logs | Customer invoice data, logs | Invoice API / logging flow | Centralized logging access controls | Medium | Redact invoice amounts, customer identifiers, and tokens from logs | App Team | Open |
| Denial of Service | Attacker submits high-volume support ticket requests to degrade the support workflow | Application availability, support ticket system | Support Ticket Form | WAF-level rate limiting | Medium | Add application-level rate limiting and alerting | App Team | Open |
| Elevation of Privilege | Standard customer user accesses admin-only user invitation workflow | Admin user management | Invite User API | Role-based access control | High | Add explicit authorization tests for admin-only workflows | App Team | In Progress |

### Risk Rating Guidance

| Risk | Guidance |
| --- | --- |
| High | Likely or easily exploitable issue that could expose restricted/confidential data, permit privilege escalation, affect many customers, or compromise a critical system |
| Medium | Plausible issue with moderate business, security, or operational impact; compensating controls may exist |
| Low | Limited impact, difficult to exploit, or already strongly controlled |

## 6. Open Risks and Risk Acceptance

**Purpose:** Track risks that are not fully mitigated before release.

**Expectations:**

- Any accepted risk must have an accountable approver.
- High risks should not be accepted without explicit leadership approval.
- Accepted risks should have an expiration or review date.

| Risk ID | Description | Risk Level | Reason Not Mitigated | Approver | Expiration / Review Date |
| --- | --- | --- | --- | --- | --- |
| R-001 |  | High / Medium / Low |  |  |  |

**Example:**

| Description | Risk Level | Reason Not Mitigated | Approver | Expiration / Review Date |
| --- | --- | --- | --- | --- |
| Support ticket form does not yet include per-user rate limits | Medium | Existing WAF rate limit provides partial protection; application-level limit scheduled next sprint | Director of Engineering | 2026-07-15 |

## 7. Review Checklist and Maintenance

**Purpose:** Confirm the threat model is complete enough to support review, release, and future updates.

**Expectations:**

- Complete this checklist before security review or production release.
- Review at least annually for active production applications.
- Update the threat model when major architecture, authentication, authorization, data, integration, or deployment changes occur.

| Check | Complete? | Notes |
| --- | --- | --- |
| Application scope is clearly defined | Yes / No |  |
| Architecture or data flow diagram is included | Yes / No |  |
| Trust boundaries are identified | Yes / No |  |
| External dependencies are documented | Yes / No |  |
| Sensitive assets and data classifications are documented | Yes / No |  |
| Entry and exit points are documented | Yes / No |  |
| STRIDE or equivalent threat identification was performed | Yes / No |  |
| High and Medium risks have mitigations or risk acceptance | Yes / No |  |
| Open mitigation work has owners and statuses | Yes / No |  |
| Security reviewer has reviewed the model | Yes / No |  |
