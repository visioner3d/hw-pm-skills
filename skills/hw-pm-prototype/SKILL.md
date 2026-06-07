---
name: hw-pm-prototype
description: "Use after design freeze to manage EVT/DVT/PVT validation phases, track defect trends, and gate production readiness."
---

# Prototype & Validation Management (hw-pm-prototype)

## Overview
Manages the full validation lifecycle from first prototype through production validation. Three sequential phases with exit criteria at each gate.

## When to Use
- Design freeze complete, phase_status is "validate"
- Prototypes built and ready for testing

## Process
1. Read PRD technical specs, generate Test Plan (spec → test method → accept criteria mapping)
2. EVT (Engineering Validation): functional completeness, >= 80% pass rate required
3. DVT (Design Validation): reliability + compliance testing, >= 90% pass, no Class 1 defects
4. PVT (Production Validation): yield >= target, first-pass yield >= 80%
5. After each phase: update defect_trend.md with phase-over-phase comparison
6. Each phase exit requires user confirmation before proceeding to next

## Output Format
Write to artifacts/phase_4/:
- test_plan.md — full test plan with acceptance criteria
- evt_report.md — EVT results + exit recommendation
- dvt_report.md — DVT results + exit recommendation
- pvt_report.md — PVT results + exit recommendation
- defect_trend.md — defect counts by phase and severity
- validation_freeze.md — final sign-off

## Hard Gate Checklist
```
[ ] Test Plan created, covering all PRD specs
[ ] EVT exit: pass rate >= 80%, no blocking issues
[ ] DVT exit: pass rate >= 90%, no Class 1 defects
[ ] PVT exit: yield >= target, first-pass >= 80%
[ ] Validation freeze signed off
[ ] User confirmed, phase_status updated to "manufacturing"
```

## Common Mistakes
- Not creating a Test Plan before testing begins
- EVT exit without verifying all blocking issues closed
- Rushing to PVT with known DVT failures
