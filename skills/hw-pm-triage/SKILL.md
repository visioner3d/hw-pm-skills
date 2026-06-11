---
name: hw-pm-triage
description: "Use across all phases (2-5) to manage risk register, issue tracking, and engineering change orders (ECO)."
---

# Risk & Issue Triage (hw-pm-triage)

## Overview

A cross-phase "second brain" for the PM. Implements four lines of defense:

| Layer | Function | Artifact | Mechanism |
|-------|----------|----------|-----------|
| **Prevent** | Risk register → early warning | `risk_register.md` | Probability × Impact, prevention_value scoring |
| **Detect** | Issue tracker → surface problems | `issue_tracker.md` | Severity (P0-P3), owner, ETA |
| **Correct** | ECO log → trace changes | `eco_log.md` | Affected parts/docs, reason, approver |
| **Remember** | Decision rationale → preserve WHY | `decisions_log.md` | Who decided what, when, based on what data |

Additionally tracks two cross-project integrity metrics:
- **Fidelity Index**: how close the current product remains to the original vision
- **Tech Debt Register**: shortcuts taken now that will become problems later

## When to Use

- Any phase after Phase 1 Go
- New risk identified
- Design or process change needed
- Issue needs escalation
- Major decision made (record rationale)

## Process

### 1. Load Existing Artifacts

Load existing `risk_register.md`, `issue_tracker.md`, `eco_log.md`, `decisions_log.md` from `artifacts/shared/`. Create if absent.

### 2. Prevent — Risk Register

Review new information against risk register. For each risk:

```markdown
| ID | Risk | Type | P (1-5) | I (1-5) | Score | Prevention Value | Mitigation | Owner | Status |
|----|------|------|---------|---------|-------|-----------------|------------|-------|--------|
| R-001 | MCU EOL within 18 months | Commercial | 3 | 5 | 15 | 25x | Pre-buy buffer stock + qualify alt | {name} | Open |
| R-002 | Enclosure warp at 50°C | Physics | 2 | 5 | 10 | N/A — must fix | Redesign rib pattern | {name} | Open |
```

**Risk Type field:**
- **Physics (deterministic)** — governed by physical law, not probability. EMI compliance, thermal limits, material properties. These CANNOT be mitigated by "monitoring" — they must be resolved or the design must change.
- **Commercial (probabilistic)** — market, supplier, competitive risks. These can be managed with contingency plans.

**Prevention Value** = `(P × I × cost_if_occurs) / cost_of_prevention`. Only applicable to Commercial risks. Prioritize items where Prevention Value > 5x.

Flags: if any risk score > `max_risk_exposure` from company.yaml, trigger alert.

### 3. Detect — Issue Tracker

Log new issues from design reviews, test failures, supplier alerts.

```markdown
| ID | Severity | Title | Phase | Owner | ETA | Status |
|----|----------|-------|-------|-------|-----|--------|
| I-001 | P0 | Regulator out of stock | Phase 3 | {name} | {date} | Open |
```

Standard P0-P3 severity. P0 issues must have owner and ETA within 48 hours.

### 4. Correct — ECO Log

When a change is approved:

```markdown
| ECO-ID | Date | Reason | Affected | Approver | Implemented |
|--------|------|--------|----------|----------|-------------|
| ECO-001 | {date} | Sub BOM item due to EOL | PCB rev B, BOM v3 | {name} | {date} |
```

### 5. Remember — Decisions Log

**Critical for organizational memory.** Hardware projects run 12-24 months — team members leave, context evaporates. Every non-obvious decision MUST be logged.

```markdown
| ID | Date | Decision | Rationale | Made By | Based On | Reversible? |
|----|------|----------|-----------|---------|----------|-------------|
| D-001 | {date} | Selected STM32H7 over NXP i.MX RT | Lower BOM, better driver maturity | {name} | EE analysis v2, supplier roadmap | Medium |
```

**Key principle:** Record why a decision was made, not just what was decided. "Why did we pick this MCU?" is the question that gets asked 6 months later when a new hire joins.

### 6. Track — Fidelity Index

At each phase exit, compare current product definition against the Phase 1 original vision. Write to `artifacts/shared/fidelity_index.md`:

```markdown
# Product Fidelity Index: {project_name}

| Phase | Date | Fidelity Score | Major Deviations | Degradation Source |
|-------|------|---------------|-----------------|-------------------|
| Phase 1 (baseline) | {date} | 100% | — | — |
| Phase 2 (PRD) | {date} | {N}% | {list} | {which decisions caused drift} |
| Phase 3 (Design) | {date} | {N}% | {list} | {which decisions caused drift} |
| Phase 4 (Validate) | {date} | {N}% | {list} | {which decisions caused drift} |
| Phase 5 (Launch) | {date} | {N}% | {list} | {which decisions caused drift} |
```

**Fidelity Score:** Start at 100%. Each deviation from the original PRD vision subtracts points weighted by impact:
- Feature removed: -5 to -15 points (depending on priority)
- Performance target relaxed >10%: -5 points
- Cost target exceeded >10%: -5 points  
- Schedule slip >25%: -3 points
- Aesthetic or UX compromise: -3 points

If fidelity_score < 70%: **"Product Integrity Alert"** — the product shipping today is materially different from what was approved at Gate 1. This may be fine (markets change) or a failure of execution (death by a thousand cuts). The PM must explicitly confirm: is the current product still worth shipping?

### 7. Track — Tech Debt Register

Hardware technical debt is invisible until it isn't. Log it early.

```markdown
| ID | Debt | Incurred | Why Accepted | If Not Fixed, Becomes P0 By | Owner |
|----|------|----------|-------------|---------------------------|-------|
| TD-001 | Skipped HALT on rev A PCB | Phase 3 | Schedule pressure, DVT will catch | Phase 4 (DVT) | {name} |
| TD-002 | Single-source connector | Phase 3 | Only supplier with required spec | Phase 5 (ramp) | {name} |
```

Each tech debt item MUST have a "becomes P0 by" date. Items without this field are wishful thinking, not managed debt.

### 8. Weekly Review

Present to PM for review:
- Top 5 risks by score, with prevention_value
- All P0/P1 open issues
- Pending ECOs not yet implemented
- Tech debt approaching its "becomes P0 by" date
- Fidelity index trend (is it dropping?)

## Output Format

Write to `artifacts/shared/`:

| File | Content |
|------|---------|
| `risk_register.md` | All risks with Type, P/I scores, Prevention Value |
| `issue_tracker.md` | Open/closed issues by severity |
| `eco_log.md` | ECO/ECN history |
| `decisions_log.md` | Decision rationale — the WHY behind every major choice |
| `fidelity_index.md` | Phase-over-phase drift from original vision |
| `tech_debt.md` | Accepted shortcuts with expiration dates |

## Hard Gate Checklist

```
[ ] Risk register exists and covers current phase
[ ] All Physics-type risks have a resolution plan (not just "monitor")
[ ] All P0 issues have owners and ETA
[ ] ECO log complete for all approved changes
[ ] Decisions log updated for all major decisions made in this phase
[ ] Fidelity index updated for current phase
[ ] Tech debt items have "becomes P0 by" dates
[ ] User reviewed open risks, P0/P1 issues, fidelity trend, and tech debt
```

## Common Mistakes

- **Letting risk register become a static document** — update weekly
- **Treating Physics risks like Commercial risks** — you cannot "mitigate" thermodynamics with a contingency plan
- **ECOs logged without implementation verification** — change was approved but never applied
- **Not logging decision rationale** — 6 months from now, nobody remembers why that component was chosen
- **Ignoring fidelity drift** — each individual compromise seems small; the cumulative effect kills products
- **Tech debt with no expiration date** — "we'll fix it later" without a date means "we'll never fix it"
