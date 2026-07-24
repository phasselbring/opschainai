---
name: governed-rag-readiness-assessment
description: Assess whether a retrieval-augmented generation system is ready for enterprise use. Use whenever a user asks whether RAG is secure, permission-aware, grounded, source-backed, current, auditable, resistant to prompt injection, or suitable for a production pilot. Review source authority, entitlement trimming, data lineage, freshness, DLP, citations, refusal behavior, evaluation, and ownership. Produce a readiness score, critical blockers, and a bounded pilot design.
compatibility: Applies to vector search, keyword search, hybrid search, graph retrieval, document assistants, and enterprise knowledge systems. Requires retrieval configuration, permission model, source inventory, and test evidence.
metadata:
  author: NPM Technologies / OpsChainAI
  version: "0.1.0"
  domain: governed-rag
  last-reviewed: "2026-07-24"
---

# Governed RAG Readiness Assessment

## Purpose

Determine whether retrieval preserves the enterprise’s authority, permissions, evidence, and accountability before content reaches a generative model.

A fluent answer is not a control. Review what was retrieved, why the user was entitled to it, whether the source is current and authoritative, and what the system does when evidence is missing or unsafe.

## Required Inputs

Collect:

- Business use case and intended users.
- Source systems and source owners.
- Document classes, data classifications, and restricted groups.
- Ingestion, parsing, chunking, enrichment, embedding, and indexing process.
- Index schema and filters.
- User identity and group/role mapping.
- Retrieval query construction and security trimming.
- Model prompts and response rules.
- DLP, redaction, external provider, and retention controls.
- Citation and source-display behavior.
- Freshness, revision, deletion, and re-indexing process.
- Evaluation set, expected answers, denial cases, and incident history.
- Logs and audit records.

Mark absent evidence as `UNVERIFIED`. Do not assume a feature exists because the platform supports it.

## Readiness Domains

Score each domain from 0 to 3.

- `0` = absent or unsafe.
- `1` = informal, manual, or prompt-only.
- `2` = implemented with important gaps.
- `3` = explicit, enforced, tested, and owned.

### Domain 1: Source Authority

Verify:

- Each corpus has a named owner.
- The system distinguishes authoritative, reference, draft, historical, and unapproved sources.
- Approved revisions can supersede old content.
- Deleted or revoked content is removed from retrieval.
- Source location and revision are available to the response.

Critical blockers:

- Draft or obsolete content is indistinguishable from approved policy.
- No one owns the corpus.
- The system cannot remove revoked content promptly.

### Domain 2: Entitlement-Aware Retrieval

Verify:

- User identity is validated.
- Groups, roles, document ACLs, row-level security, or equivalent entitlements are translated into retrieval filters.
- Filters are applied before documents or chunks reach the model.
- The application cannot silently omit the filter.
- Partner, customer, legal, HR, executive, export-controlled, or other restricted content remains separated.

Critical blockers:

- All users search a flattened shared index with no permission filter.
- Restricted content is retrieved and then hidden only by the model.
- An application admin key bypasses the user’s entitlement context.

### Domain 3: Ingestion and Data Lineage

Verify:

- Every chunk can be traced to source, document, section, revision, and ingestion time.
- Parsing preserves material structure such as headings, tables, footnotes, and document relationships.
- Duplicate and conflicting sources are handled.
- OCR or extraction quality is measured when used.
- Sensitive metadata is not unintentionally exposed.

Critical blockers:

- The organization cannot identify which source produced an answer.
- Parsing materially changes meaning without detection.

### Domain 4: Freshness and Change Control

Verify:

- Source changes trigger re-indexing within a defined interval.
- The system records source and index timestamps.
- Stale content can be detected and excluded.
- Revision conflicts have a deterministic rule.
- Index schema, embedding model, chunking, and prompt changes are versioned.

Critical blockers:

- The system presents stale policy as current.
- No process exists to propagate revocation or correction.

### Domain 5: Prompt-Injection and Content Handling

Verify:

- Retrieved text is treated as data, not trusted instruction.
- Tool calls and data access remain policy-bound outside the model.
- Documents cannot redefine system authority.
- Suspicious instructions, links, or encoded content are detected or isolated where appropriate.
- Retrieved content cannot trigger uncontrolled external actions.

Critical blockers:

- A document can instruct the model to invoke arbitrary tools.
- Tool authority depends only on the model ignoring malicious text.

### Domain 6: DLP and Model Boundary

Verify:

- Sensitive user input is detected before the model call.
- Sensitive retrieved content is allowed only for entitled users and approved providers.
- Redact, block, hold, and approval actions are defined.
- Provider, region, retention, and training settings are policy-controlled.
- Keys and credentials are injected server-side and not exposed to the model or client.

Critical blockers:

- Regulated or confidential content can be sent to an unapproved provider.
- DLP occurs after disclosure.

### Domain 7: Grounding, Citation, Refusal, and Escalation

Verify:

- Answers identify supporting sources and revisions.
- Claims are limited to retrieved evidence.
- The system refuses when approved evidence is insufficient.
- Conflicting sources are disclosed or escalated.
- High-impact questions route to named human owners.
- The response distinguishes quotation, paraphrase, inference, and recommendation when material.

Critical blockers:

- The system invents policy or factual claims without supporting sources.
- Users cannot inspect the evidence.

### Domain 8: Evaluation and Audit

Verify:

- The evaluation set includes authorized answers, denied retrieval, stale sources, conflicting sources, prompt injection, missing evidence, and refusal cases.
- Retrieval quality and answer quality are measured separately.
- Permission leakage is tested directly.
- Logs record user identity, filter, sources retrieved, model, response status, and policy result as appropriate.
- Incidents can be investigated without reconstructing events from memory.

Critical blockers:

- No entitlement-leak test exists.
- The organization cannot determine which restricted content reached the model.

## Readiness Decision

Maximum score: 24.

- `READY FOR BOUNDED PILOT`: 20–24, no critical blockers.
- `READY WITH CONDITIONS`: 16–19, no critical blockers, remediation owned and time-bound.
- `REMEDIATE BEFORE PILOT`: 10–15 or important evidence is missing.
- `NOT READY`: 0–9 or any unresolved critical blocker.

A high score does not override a critical blocker.

## Required Output

```markdown
# Governed RAG Readiness Assessment

## Executive Decision
- Use case:
- Users:
- Corpus:
- Decision: READY FOR BOUNDED PILOT | READY WITH CONDITIONS | REMEDIATE BEFORE PILOT | NOT READY
- Score: __ / 24
- Critical blockers:
- Confidence:

## Readiness Scorecard
| Domain | Score | Evidence | Gap | Required action | Owner |
|---|---:|---|---|---|---|

## Retrieval and Permission Flow
1. User identity
2. Entitlement mapping
3. Query and filter
4. Retrieved sources
5. Model boundary
6. Response, citation, refusal, or escalation
7. Audit record

## Critical Findings

## Bounded Pilot Design
- Approved users:
- Approved sources:
- Excluded data:
- Provider and region:
- Required filters:
- Required citations:
- Refusal conditions:
- Human escalation:
- Evaluation set:
- Success measures:
- Stop conditions:

## Unverified Assumptions
```

## Minimum Evaluation Scenarios

1. Authorized user receives authorized source.
2. Unauthorized user requests restricted source.
3. User belongs to multiple groups with mixed permissions.
4. Approved source has been superseded.
5. Two authoritative sources conflict.
6. Retrieved document contains prompt injection.
7. User input contains sensitive data.
8. Retrieval returns no adequate evidence.
9. Citation points to the wrong revision.
10. A restricted document is deleted or access is revoked.
11. Model provider is removed from policy.
12. Audit or index service is unavailable.

## Known Limitations

This assessment does not prove model truthfulness or eliminate all prompt-injection risk. It determines whether retrieval, authority, evidence, and execution are bounded sufficiently for the stated use case.
