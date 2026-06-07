---
name: hw-pm-triage
description: "Use across all phases (2-5) to manage risk register, issue tracking, and engineering change orders (ECO)."
---

# Risk & Issue Triage (hw-pm-triage)

## Overview
A cross-phase "second brain" for the PM. Implements a three-layer defense: Prevent (risk register → early warning), Detect (issue tracker → surface problems), Correct (ECO log → trace changes).

## When to Use
- Any phase after Phase 1 Go
- New risk identified
- Design or process change needed
- Issue needs escalation

## Process
1. Load existing risk_register.md, issue_tracker.md, eco_log.md (create if absent)
2. **Prevent:** Review new information against risk register. Add new risks (probability × impact × response strategy). Flag if risk score exceeds threshold.
3. **Detect:** Log new issues from design reviews, test failures, supplier alerts. Assign severity (P0-P3), owner, ETA.
4. **Correct:** When a change is approved, log ECO: affected parts/docs, reason, approver, implementation date.
5. Weekly: present open P0/P1 issues, overdue risks, pending ECOs for user review.
6. Archive closed items with closure notes.

## Output Format
Write to artifacts/shared/:
- risk_register.md — all risks with P/I scores
- issue_tracker.md — open/closed issues
- eco_log.md — ECO/ECN history

## Hard Gate Checklist
```
[ ] Risk register exists and covers current phase
[ ] All P0 issues have owners and ETA
[ ] ECO log complete for all approved changes
[ ] User reviewed open risks and P0/P1 issues
```

## Common Mistakes
- Letting risk register become a static document (update weekly)
- No severity classification — every issue looks equally urgent
- ECOs logged without implementation verification (change was approved but never applied)
