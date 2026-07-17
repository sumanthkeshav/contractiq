# ContractIQ - Product Requirements Document

## Vision

ContractIQ is a Claude Code-powered CLI tool that handles the full in-house legal
contract lifecycle end to end: **draft -> redline -> extract hotspots -> insert to
system of record -> track obligations** - with no step requiring manual copy-paste
between disconnected tools.

Built for an in-house legal team at a ~500-person SaaS company: small enough that
every lawyer's hour matters, large enough that the contract portfolio has outgrown
anyone's memory. See **Problem** below for the five pain points this addresses.

Non-negotiable accuracy rule across every use case: **never fabricate contract
language or terms. Missing fields return null, never a guess. Every extracted term
cites its source paragraph. Ambiguous language is flagged, never silently interpreted.**

---

## North Star Metric

**Hours of legal-team time reclaimed from contract admin per month** - the single
number every use case rolls up into: drafting time saved + redlining time saved +
extraction time saved + obligation-tracking time saved, minus actual human review
time spent using ContractIQ's output. Ties directly back to the core problem below
(60-70% of legal time lost to low-value admin).

Two guardrail metrics accompany it, so speed is never bought by carelessness:
- **Zero fabricated contract terms** - a hard floor, not a trend line
- **Zero missed obligation deadlines** - the tracker exists specifically to prevent this

---

## Problem

1. **Boilerplate drafting eats disproportionate lawyer time.** At 500 people, deal
   volume (NDAs, MSAs, SOWs) is high enough to matter but the legal team is usually
   still just 2-5 people. NDAs and MSAs are 85-90% identical clause-for-clause every
   time, yet each one gets drafted from a stale template or a prior deal's redline,
   chased down via Slack. Every hour spent on this is an hour not spent on the
   10-15% that's actually novel risk.

2. **Redlining/negotiation turnaround is slow and inconsistent.** Comparing a
   counterparty's paper against your standard positions is manual, clause-by-clause,
   over email threads with files named `MSA_v3_FINAL_v2_KM_edits.docx`. Different
   lawyers apply the playbook slightly differently depending on who's free that week,
   so the business sees inconsistent turnaround and inconsistent risk tolerance -
   both erode trust in legal as a partner rather than a blocker.

3. **Key terms are buried and unqueryable at portfolio scale.** Nobody can quickly
   answer "which active contracts have an uncapped indemnity" or "what's our
   aggregate liability exposure this quarter" because payment terms, SLAs, liability
   caps, and IP clauses live inside PDFs, not a structured system. This becomes
   acute (and expensive) under time pressure - an M&A due diligence sprint or a
   board request turns into weeks of manual spreadsheet-building instead of a query.

4. **Obligations and deadlines slip silently.** Auto-renewal opt-out windows,
   notice periods, quarterly SLA reviews - these are time-bound commitments with no
   owner or calendar once the deal closes. At 500 people the contract count has
   usually outgrown any single person's memory, so the first sign of a missed
   60-day opt-out window is often sales asking why a bad vendor auto-renewed.

5. **Legal is a disconnected bottleneck relative to company scale.** Contracts, the
   CRM (Salesforce), ticketing (Jira), storage (Drive), and comms (Slack/email) are
   all separate systems of record. Legal ends up doing manual data entry and
   status-chasing between them, and the business routes around legal when turnaround
   feels too slow - which is worse for risk, not better.

---

## Glossary

- **NDA (Non-Disclosure Agreement)** - protects confidential information shared
  between parties before/during a deal.
- **MSA (Master Service Agreement)** - the overarching contract that sets general
  terms (liability, payment, IP ownership, termination) governing an ongoing
  relationship between two companies.
- **SOW (Statement of Work)** - a specific, scoped-down document under an MSA that
  defines a particular project's deliverables, timeline, and cost - companies often
  sign one MSA and then many SOWs under it over time.
- **DPA (Data Processing Agreement)** - governs how a vendor processes personal
  data on a company's behalf; referenced by the curriculum but not a drafting
  target for ContractIQ v1.0 (see Out of Scope).

---

## Use Cases

### UC1 - Draft an NDA from a one-paragraph brief
**As** in-house counsel, **I want** to generate a complete NDA from a short deal
description **so that** I stop re-drafting the same 12 clauses from scratch.
- **Input:** party names, jurisdiction, term length, mutual vs one-way
- **Output:** complete NDA in markdown, all 12 clause blocks present, no placeholder tokens
- **Target:** under 60 seconds, ready for attorney review

### UC2 - Draft an MSA or SOW from a one-paragraph brief
**As** in-house counsel, **I want** to generate an MSA/SOW from a deal brief **so
that** standard commercial agreements don't each consume 2-3 hours of drafting time.
- **Input:** services description, payment terms, IP ownership stance, liability cap,
  (SOW only: scope/deliverables/milestones/acceptance criteria)
- **Output:** complete MSA or SOW in markdown, all required clause blocks present
- **Target:** under 60 seconds, ready for attorney review

### UC3 - Redline a counterparty contract against the playbook
**As** in-house counsel, **I want** any incoming contract compared clause-by-clause
against our standard positions **so that** I can see every deviation and its risk
level without manually cross-referencing a playbook.
- **Input:** incoming contract text, applicable playbook (MSA or NDA)
- **Output:** deviation table - standard position, counterparty position, deviation
  yes/no, risk level (HIGH/MED/LOW) with rationale, recommended response
- **Target:** under 5 minutes per contract, verified accurate by a human attorney

### UC4 - Extract structured key terms from a single contract
**As** legal ops, **I want** the 22 hotspot fields (payment terms, SLAs, renewal
dates, liability caps, etc.) pulled as structured JSON **so that** I don't manually
build extraction spreadsheets.
- **Input:** a single contract (text or PDF, chunked if over ~15 pages)
- **Output:** JSON with all 22 fields, each populated field carrying a citation
  (paragraph number + excerpt) or `null` if genuinely absent - never inferred
- **Target:** near-zero hallucination rate, spot-checkable against source

### UC5 - Extract across a contract portfolio in parallel (due-diligence scale)
**As** legal ops running M&A due diligence, **I want** 50-200 contracts extracted
in parallel **so that** a 48-hour diligence window is achievable instead of impossible.
- **Input:** a directory of contracts
- **Output:** `master_portfolio.json` - one entry per contract, all 22 fields,
  `contract_id`, `extraction_timestamp`, `field_null_count`
- **Target:** wall-clock time materially lower than sequential processing of the same set

### UC6 - Insert extracted terms, track obligations, and broadcast status automatically
**As** legal ops (and the business stakeholders waiting on them), **I want**
extracted terms to land in our system of record automatically, get alerted before
deadlines, and have every new contract's status visible in Slack/GitHub the moment
it arrives **so that** nothing requires manual data entry, no renewal window slips
through unnoticed, and the business never has to ping legal to ask "where's my redline."
- **Input:** extracted contract JSON and `master_portfolio.json` for the weekly
  deadline sweep; a new contract pushed to `contracts/` for the arrival trigger
- **Output:**
  - *Weekly schedule:* tiered Slack alert (URGENT <30 days / ACTION NEEDED 30-60 /
    WATCH 60-90) for any contract with a renewal or expiry date inside the next 90 days
  - *On contract arrival (no human trigger):* system-of-record entry created/updated
    (dedup on contract name), a Slack summary (deviation count, extraction
    completeness), and a GitHub issue titled `Contract Review: {filename}` with full findings
- **Target:** zero-touch insert; weekly sweep with no manual trigger; under 5 minutes
  from contract arrival to status visible in Slack/GitHub/Notion - directly closes
  pain point 5 (legal as a disconnected bottleneck)

---

## Definition of Done (v1.0)

- All 6 use cases above work end-to-end via `run_pipeline.sh` on a real (or realistic
  sample) contract, in under 5 minutes total per contract
- Extraction is null-safe and cited on every field - verified by manual spot-check,
  never observed to fabricate a value
- Redline output has been reviewed and confirmed accurate by a human lawyer at least once
- MCP integrations (Drive, Slack, and one system of record) are live and tested with
  real round-trip data, not mocked
- CI pipeline auto-runs extraction + redline on any new contract pushed to `contracts/`
- Obligation tracker runs on a real schedule (not manually triggered) and has produced
  at least one real tiered Slack digest
- Security: `allowedPaths` scoped to this project only, audit log capturing every
  tool access, no credentials committed to git
- A second person (not the builder) can install and run the tool from `INSTALL.md`
  alone, without help

## Out of Scope (v1.0)

- Drafting covers **NDA, MSA, and SOW only** - no DPA, employment agreement,
  vendor terms, or other contract-type generation. (Redlining and extraction can
  still process an incoming DPA or other contract type as general text input -
  it's only the drafting templates that are limited to these three.)
- Not a full CLM (contract lifecycle management) platform replacement - no
  contract repository UI, no e-signature, no clause library management UI
- Not a substitute for attorney legal judgment - every output (draft, redline,
  extraction) is reviewed by a human before it's relied on
- No litigation, dispute, or post-signature amendment tracking beyond the
  obligation/deadline sweep
- English-language contracts only
- No mobile app or end-user web UI - CLI + Slack/Notion/Drive as the only surfaces
- No real-time collaborative redlining (e.g., simultaneous multi-editor markup) -
  redline output is a report, not a live editing session
- Single-tenant / single-org use only - no multi-tenant or client-facing deployment