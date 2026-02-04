# Revised syllabus (non-AI SaaS)

This syllabus uses CS2023 knowledge areas as the conceptual backbone for “how software works” and SWEBOK v4 as the practical backbone for “how software gets built, tested, shipped, and operated.”

Deliverables are PM-friendly: PRD slices, data contracts, threat-model notes, and “definition of done” checklists that you validate against what Cursor/Claude Code generates.

## Product requirements + NFRs
**Core:** functional vs non-functional requirements, explicit constraints (latency, cost, availability), edge cases, acceptance criteria.​
SWEBOK: Software Requirements; Software Engineering Management.​

## Domain modeling + data design
**Core:** entities/relationships, invariants, relational schema basics, transactions, indexing intuition, auditability/event logging.​
SWEBOK: Software Architecture (new KA), Software Design.​

## Web architecture (Next.js mental model)
**Core:** browser/server boundaries, SSR/CSR tradeoffs, routing, API surface design, caching concepts, error handling semantics.​
SWEBOK: Architecture (new KA), Construction, Quality.​

## Authentication + sessions (choose an approach)
Option B (BaaS-native): Supabase Auth with Next.js App Router helpers (session handling patterns geared for Next.js).​
SWEBOK: Security (new KA), Construction.​

## Authorization (RBAC) + multi-tenancy
**Core:** roles/permissions, workspace boundaries, “who can see what,” least privilege, admin flows, audit log requirements.​
SWEBOK: Security (new KA), Architecture (new KA).​

## Payments + subscriptions
**Core:** pricing model decisions (trial, monthly/annual), checkout flow, webhooks, entitlement mapping (“paid plan unlocks features”), cancellations/refunds, customer portal.
SWEBOK: Requirements, Operations (new KA), Management.​

## Testing strategy (confidence, not perfection)
**Core:** unit vs integration vs e2e mental models, test pyramid intuition, “what must never break” lists, regression prevention via CI gates.​
SWEBOK: Software Testing; Software Quality.​

## Release engineering + configuration
**Core:** environments (dev/staging/prod), migrations, secrets, feature flags, versioning, rollbacks, Git/GitHub workflow discipline.​
SWEBOK: Software Configuration Management; Operations (new KA).​

## Observability + reliability basics
**Core:** what to log, what to measure, basic SLO thinking, alerts that map to user impact, incident playbooks.​
If you want a free, canonical reliability reference, Google’s SRE book is available online.​

## Security baseline (practical checklists)
**Core:** OWASP Top 10 awareness (common web app failure classes) and a “secure dev habits” checklist you can apply to every PR.​
For process-level secure development practices, NIST’s SSDF (SP 800-218) is a free reference you can translate into lightweight “done criteria.”​