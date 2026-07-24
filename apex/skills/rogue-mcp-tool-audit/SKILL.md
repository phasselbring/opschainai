---
name: rogue-mcp-tool-audit
description: Inventory and assess MCP servers, MCP clients, agent tools, plugins, functions, connectors, and other AI capability sources for enterprise approval. Use whenever a user asks whether MCP is governed, which AI tools are unapproved, what permissions an agent has, whether a server is rogue, how to build an MCP registry, or how to reduce agent blast radius. Produce a capability registry, risk classification, and approve, remediate, quarantine, or reject decision for each item.
compatibility: Requires access to deployment inventories, configuration, identities, permissions, network paths, tool schemas, and audit evidence. Do not perform unauthorized discovery or exploitation.
metadata:
  author: NPM Technologies / OpsChainAI
  version: "0.1.0"
  domain: mcp-and-agent-governance
  last-reviewed: "2026-07-24"
---

# Rogue MCP & Unapproved Tool Audit

## Purpose

Create an accountable inventory of the capabilities AI systems can invoke and determine whether each capability is registered, owned, least-privileged, policy-bound, and auditable.

Treat an MCP server or tool as a capability source, not as trustworthy merely because it uses a standard protocol.

## Safety Boundary

This skill is for authorized enterprise inventory and governance.

Do not:

- Scan systems without authorization.
- Attempt credential theft, exploitation, or persistence.
- Execute tools merely to discover what they do when configuration and code inspection are available.
- Publish sensitive endpoints, credentials, or vulnerabilities outside the approved audience.

## Required Inputs

Collect:

- MCP clients and agent runtimes.
- MCP server names, owners, repositories, deployment locations, versions, and environments.
- Tool names, descriptions, schemas, and actual implementation paths.
- Authentication method and workload identity.
- Secrets, tokens, API keys, or managed identities used.
- Network ingress and egress.
- Data sources and external destinations.
- Read, write, delete, send, publish, execute, and administrative capabilities.
- User approval and workflow approval requirements.
- Logs, policy decisions, errors, and kill-switch procedures.
- Change and release process.

Mark every unknown field as `UNKNOWN`. Unknown authority is a finding, not a blank.

## Audit Procedure

### Step 1: Build the Capability Registry

Create one record for each client, server, and tool.

Required fields:

| Field | Description |
|---|---|
| Capability ID | Stable unique identifier |
| Client / runtime | Where the request originates |
| Server | MCP or equivalent capability host |
| Tool | Callable operation |
| Owner | Accountable person or team |
| Environment | Dev, test, production |
| Identity | User, app, service account, managed identity |
| Data / resource | What it can reach |
| Authority | Read, write, delete, send, publish, execute, admin |
| External egress | Destination and purpose |
| Approval | None, user confirmation, workflow approval, dual control |
| Audit | Events recorded and retention |
| Status | Approved, remediate, quarantine, reject, unknown |

### Step 2: Verify Ownership and Registration

Check:

- Named business and technical owner.
- Approved repository and release source.
- Version pinning or controlled update path.
- Environment registration.
- Documented purpose and intended users.
- Dependency and supply-chain ownership.

Quarantine when ownership or provenance cannot be established.

### Step 3: Verify Identity and Secret Handling

Check:

- User identity propagation where required.
- Workload identity for server-to-resource access.
- Least-privilege permissions.
- Secret storage outside prompts, source code, local configuration, and model context.
- Token audience, lifetime, rotation, and revocation.
- Separation between development and production identities.

Critical findings:

- Shared broad credentials.
- Secrets reachable by the model.
- Production access using a developer identity.
- A server can assume an identity unrelated to the initiating user without policy.

### Step 4: Verify Tool Scope and Input Validation

For each tool:

- Compare the description with actual behavior.
- Identify optional parameters that expand authority.
- Confirm allowlisted operations and constrained input shapes.
- Reject arbitrary SQL, shell, code execution, file paths, URLs, or destinations unless explicitly required and separately controlled.
- Determine whether a read tool can mutate state through side effects.
- Determine whether a tool can chain into broader tools.

Critical findings:

- A benign description masks broad implementation authority.
- The caller can select arbitrary destinations, queries, commands, or resources.
- Tool schemas omit constraints enforced only by natural-language instructions.

### Step 5: Verify Data and Egress Boundaries

Check:

- Data classification and allowed users.
- Resource-level permissions.
- Network and tenant boundary.
- External hosts, webhooks, model providers, email, messaging, and file transfers.
- DLP, redaction, block, and hold behavior.
- Retention by external services.

Critical findings:

- Unapproved external egress.
- Sensitive data can leave without review.
- A tool can enumerate or export an entire data plane.

### Step 6: Verify Human Approval

Identify actions requiring confirmation or approval:

- External communication.
- Financial commitment.
- Customer or employee impact.
- Production changes.
- Delete, publish, transfer, or execute.
- Regulatory, legal, safety, or quality decisions.

Verify approval is enforced by workflow or policy, not solely by a prompt instruction.

### Step 7: Verify Audit and Kill Switch

Confirm the organization can answer:

- Who invoked the tool?
- Which client and server version handled it?
- What policy applied?
- What resource was accessed?
- What action occurred?
- What was allowed, denied, held, or failed?
- Can the tool, server, identity, or route be disabled quickly?

Critical findings:

- No record of write, send, delete, or execute actions.
- Disabling the agent does not disable the underlying credential or tool.

### Step 8: Classify Each Capability

Use:

- `APPROVED`: registered, owned, least-privileged, bounded, tested, and audited.
- `APPROVED WITH CONDITIONS`: limited remediation with named owner and due date.
- `REMEDIATE`: intended use is valid, but controls are incomplete.
- `QUARANTINE`: unknown owner, provenance, authority, dependency, or behavior.
- `REJECT`: deceptive, malicious, unnecessary broad authority, or irreconcilable control failure.

Default to quarantine when ownership, identity, or capability is unknown.

## Risk Tiers

- `TIER 0 — Informational`: no enterprise data or action authority.
- `TIER 1 — Bounded Read`: approved sources, permission-trimmed, no external send.
- `TIER 2 — Sensitive Read / Internal Write`: controlled data or reversible internal action.
- `TIER 3 — External or Material Action`: send, publish, production write, financial, customer, employee, legal, safety, or regulatory impact.
- `TIER 4 — Administrative / Unbounded`: arbitrary code, shell, raw production access, credential access, security administration, or broad export. Reject or redesign unless exceptional controls and explicit ownership exist.

## Required Output

```markdown
# MCP and Agent Tool Governance Audit

## Executive Summary
- Systems reviewed:
- Registered capabilities:
- Unknown capabilities:
- Critical findings:
- Recommended immediate action:

## Capability Registry
| ID | Client | Server | Tool | Owner | Identity | Resource | Authority | Egress | Approval | Audit | Tier | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|

## Critical Findings
| ID | Finding | Exposure | Immediate containment | Long-term control | Owner |
|---|---|---|---|---|---|

## Unapproved and Unknown Capabilities

## Remediation Roadmap
### 0–24 Hours
### 2–10 Days
### 30 Days

## Required Registry and Approval Workflow

## Unverified Assumptions
```

## Minimum Evaluation Scenarios

1. Unregistered server attempts a tool call.
2. Registered server invokes an unapproved tool.
3. Approved tool requests a resource outside its scope.
4. A user without entitlement requests restricted data.
5. A tool input contains an arbitrary URL or shell command.
6. A prompt-injected document requests external exfiltration.
7. A production write lacks human approval.
8. A provider or destination is removed from policy.
9. Audit storage is unavailable.
10. The server or identity is revoked during execution.

## Known Limitations

This audit finds documented and observable capabilities. It does not prove that no shadow deployment exists and does not replace code review, dependency analysis, cloud-security posture management, or authorized security testing.
