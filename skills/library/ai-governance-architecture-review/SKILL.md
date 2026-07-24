---
name: ai-governance-architecture-review
description: Review an enterprise AI architecture, agent, assistant, RAG system, MCP deployment, or AI workflow for governance and control gaps. Use whenever a user asks what an AI system can read, what users can send, what the AI can touch or change, how identity and permissions are enforced, whether controls sit outside the model, or what should be remediated before production. Produce a capability-surface map, prioritized findings, and a bounded pilot or remediation plan.
compatibility: Requires architecture, identity, data, tool, policy, and audit information. Works across cloud, on-premises, and hybrid environments.
metadata:
  author: NPM Technologies / OpsChainAI
  version: "0.1.0"
  domain: enterprise-ai-governance
  last-reviewed: "2026-07-24"
---

# AI Governance Architecture Review

## Purpose

Determine whether an enterprise AI system operates inside explicit, enforceable, observable boundaries.

Evaluate the architecture, not the model’s promises. Prompt instructions may shape behavior, but identity, policy, permissions, data access, approval, and audit should be enforced outside the model whenever the consequence matters.

## Required Inputs

Obtain as many of the following as possible:

- Business objective and intended users.
- Architecture diagram and data flow.
- Model providers, endpoints, deployment locations, and model-routing logic.
- Agent frameworks, MCP clients, MCP servers, tools, plugins, functions, and scripts.
- Data sources, indexes, file stores, databases, ERP/CRM systems, APIs, and external services.
- User identity, application identity, service accounts, secrets, tokens, and managed identities.
- Roles, groups, resource permissions, network boundaries, and tenant boundaries.
- Prompt and DLP controls.
- Human approval points.
- Logging, audit, evaluation, incident response, and kill-switch procedures.
- Data retention and model-training settings.

Do not guess about missing controls. Mark them `UNVERIFIED`.

## Six Governance Questions

### 1. What Can the Model Read?

Inspect:

- Retrieval sources and index scope.
- Permission trimming before retrieval.
- Source authority, lineage, freshness, and revision.
- Whether restricted content is retrieved and later hidden, or excluded before the model sees it.
- Prompt-injection handling in documents, email, web pages, and tool output.
- Whether system prompts, credentials, secrets, or hidden instructions are exposed.

Preferred control:

> The model cannot disclose content it never retrieved.

Critical findings:

- A shared index flattens the enterprise permission model.
- An application admin key bypasses user entitlements.
- Restricted sources are retrieved and filtered only in generated text.

### 2. What Can Users Send?

Inspect:

- PII, PHI, PCI, financial, legal, export-controlled, confidential, and customer data handling.
- DLP before the request leaves the enterprise boundary.
- Redact, block, hold, and approval behavior.
- Allowed model providers and regions.
- File-upload and connector behavior.
- Retention and model-training terms.

Preferred control:

> Sensitive content is blocked, redacted, or held before it reaches a model endpoint.

Critical findings:

- Users can send regulated or confidential data to unapproved providers.
- DLP runs only after the model receives the prompt.
- Provider keys are embedded in client applications.

### 3. What Can the AI Touch?

Inspect every tool and action:

- Read, query, create, update, delete, approve, send, publish, transfer, execute, or administer.
- Raw SQL, arbitrary code, shell access, file-system access, web browsing, and external posting.
- Tool allowlists and input validation.
- Managed identity, least privilege, RBAC, VNet, and resource-level enforcement.
- Separation between intent interpretation and deterministic execution.

Preferred control:

> The model selects or proposes an approved intent; the architecture validates and executes a bounded operation.

Critical findings:

- Broad credentials are reachable by the model.
- The model generates unrestricted SQL or code against production.
- A tool can send externally without explicit approval.
- A service account has materially more access than the use case requires.

### 4. What Can the AI Decide?

Inspect:

- Decision consequence and reversibility.
- Financial, legal, employment, safety, customer, regulatory, or reputational reliance.
- Confidence or evidence thresholds.
- Human approval and separation of duties.
- Refusal and escalation behavior.
- Whether users are informed when they are interacting with AI where required.

Preferred control:

> The system answers when evidence supports it, refuses when it does not, and escalates when a human owns the decision.

Critical findings:

- The AI makes a high-impact decision without accountable human review.
- The system invents unsupported conclusions rather than refusing.
- Approval is represented as a prompt instruction but not enforced by workflow state.

### 5. What Evidence Remains?

Inspect whether the system records:

- User and application identity.
- Skill, agent, model, prompt version, and policy version.
- Tool and data source.
- Permission and entitlement context.
- Allow, deny, redact, hold, refuse, or escalate decision.
- Inputs, outputs, citations, errors, and approval state as appropriate.
- Cost, token usage, and latency when operationally relevant.

Preferred control:

> Audit is part of the call, not a report reconstructed after an incident.

Critical findings:

- The organization cannot determine what the AI accessed or changed.
- Logs omit denied and failed calls.
- Audit records contain unprotected secrets or unnecessary sensitive content.

### 6. Who Owns the System?

Identify:

- Business sponsor.
- Product owner.
- Data owner.
- Security and privacy owners.
- Model and prompt owner.
- Tool and integration owner.
- Incident owner.
- Review cadence, change control, and retirement path.

Critical findings:

- No named owner can accept or remediate the risk.
- Models, prompts, tools, or data sources change without review.
- No kill switch or rollback path exists.

## Architecture Control Layers

Review these layers independently. Do not assume one control compensates for all others.

1. Trust boundary and API gateway.
2. User and workload identity.
3. Policy and approved capability registry.
4. Input validation and DLP.
5. Managed identity and secret management.
6. Resource RBAC and network boundary.
7. Deterministic execution or constrained tool use.
8. Source-backed response, refusal, and escalation.
9. Audit, evaluation, monitoring, and incident response.

## Finding Severity

- `CRITICAL`: broad or hidden authority, exposed credentials, unrestricted production access, unapproved egress, or high-impact autonomous decision.
- `HIGH`: control exists only in prompt text, incomplete identity enforcement, missing permission trimming, missing approval enforcement, or materially incomplete audit.
- `MEDIUM`: ownership, testing, versioning, retention, or monitoring gap that does not immediately create broad authority.
- `LOW`: documentation, consistency, or efficiency improvement.

## Required Output

```markdown
# Enterprise AI Governance Architecture Review

## Executive Summary
- Business objective:
- Architecture reviewed:
- Overall posture: RED | AMBER | GREEN | UNVERIFIED
- Production recommendation: GO | GO WITH CONDITIONS | HOLD | STOP
- Most important reason:

## Capability-Surface Map
| Surface | Capability | Identity | Data / resource | Read / write / send / decide | Enforcement point | Audit | Owner |
|---|---|---|---|---|---|---|---|

## Six Governance Questions
| Question | Current state | Evidence | Gap | Severity |
|---|---|---|---|---|

## Priority Findings
| ID | Finding | Risk | Required control | Owner | Target date |
|---|---|---|---|---|---|

## Recommended Architecture

## Bounded Pilot Scope
- Approved users:
- Approved data:
- Approved tools:
- Prohibited actions:
- Human approval:
- Required audit:
- Success measures:
- Stop conditions:

## Unverified Assumptions
```

## Refusal and Escalation

Do not approve production when credentials, permissions, data flows, external providers, or write capabilities are unknown.

Escalate high-impact decisions to accountable business, security, privacy, legal, compliance, or safety owners.

## Minimum Evaluation Scenarios

1. Authorized user requests authorized data.
2. Unauthorized user requests restricted data.
3. A retrieved document contains prompt injection.
4. A user submits sensitive data to an external model.
5. The model requests an unapproved tool.
6. An approved tool receives an invalid parameter.
7. A direct database operation is attempted outside the approved path.
8. Evidence is insufficient and the system should refuse.
9. A human approval is denied or times out.
10. A model or provider is removed from policy.

## Known Limitations

This review evaluates documented and demonstrated controls. It does not certify regulatory compliance, prove the absence of unknown systems, or replace independent security testing.
