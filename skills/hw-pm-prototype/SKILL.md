---
name: hw-pm-prototype
description: "Use after design freeze to manage EVT/DVT/PVT validation phases, track defect trends, and gate production readiness."
---

# Prototype & Validation Management (hw-pm-prototype)

## Overview

Manages the full validation lifecycle from first prototype through production validation. Three sequential phases — EVT (Engineering Validation Test), DVT (Design Validation Test), PVT (Production Validation Test) — each with quantified exit criteria. This skill consumes the PRD technical specs and design freeze outputs, and gates each validation phase before advancing.

`hw-pm-prototype` answers: *"Does the product meet spec and can it be manufactured at target yield?"*

## When to Use

- Design freeze complete, `phase_status` is `"validate"`
- Prototypes built and ready for testing
- Need to track defect trends across validation phases

**Don't use when:**
- Design freeze not complete (finish `hw-pm-design-review` first)
- Still in Phase 1-2 (research or PRD phase)
- You only need certification tracking (use `hw-pm-cert`, which runs in parallel)

## Defect Classification

Before executing any test phase, establish defect severity levels:

| Class | Definition | Action |
|-------|-----------|--------|
| **Class 1 (Critical)** | Safety hazard, regulatory violation, or renders product unusable | Blocks phase exit. Must fix and re-verify before proceeding. |
| **Class 2 (Major)** | Functional degradation or spec violation that affects core use case | Must have fix plan with owner + ETA before phase exit. |
| **Class 3 (Minor)** | Cosmetic, UI polish, or edge-case behavior outside core use case | Tracked. Resolve at lead's discretion. Does not block exit. |

## Process

### Step 1: Generate Test Plan

Read PRD technical specs from `artifacts/phase_2/technical_spec.md` and functional requirements from `artifacts/phase_2/prd.md`. Map each spec item to a test method with quantifiable acceptance criteria.

Write `artifacts/phase_4/test_plan.md`:

```markdown
# Test Plan: {project_name}

## Coverage Matrix

| Spec Ref | Requirement | Test Method | Acceptance Criteria | Phase | Owner |
|----------|-------------|-------------|-------------------|-------|-------|
| FR-001 | Part detection within 200ms | Bench test with oscilloscope | Latency ≤200ms in 100 trials | EVT | {name} |
| NFR-004 | Operating temp 0°C-50°C | Environmental chamber | All FRs pass at 0°C, 25°C, 50°C | DVT | {name} |

## Per-Phase Test Scope

| Phase | Tests | Sample Size | Duration | Location |
|-------|-------|-------------|----------|----------|
| EVT | Functional, basic electrical | 5-10 units | 2 weeks | Engineering lab |
| DVT | Reliability, environmental, EMC, safety | 20-50 units | 4-6 weeks | Lab + external |
| PVT | Production yield, process capability | 100-200 units | 2-4 weeks | Production line |
```

### Step 2: EVT — Engineering Validation Test

**Objective:** Prove functional completeness. Does the design work at all?

**Entry criteria:**
- [ ] Design freeze signed off (all 4 domains)
- [ ] EVT prototype units built (5-10 units)
- [ ] Test plan approved
- [ ] Test equipment calibrated

**Test scope:**
- Functional test of all FRs from PRD
- Basic electrical (power consumption, voltage rails, signal integrity)
- Mechanical fit (enclosure assembly, connector alignment)
- Firmware boot and basic IO (all interfaces respond)

**Exit criteria:**
- [ ] Overall pass rate ≥ 80%
- [ ] No Class 1 defects open
- [ ] All critical FRs (Must-have) passing
- [ ] EVT defect list with root cause for every failure

Write `artifacts/phase_4/evt_report.md`:

```markdown
# EVT Report: {project_name}

**Date:** {date} | **Units tested:** {N} | **Test duration:** {days}

## Results Summary

| Category | Pass | Fail | Pass Rate | Notes |
|----------|------|------|-----------|-------|
| Functional | {N} | {N} | {%} | |
| Electrical | {N} | {N} | {%} | |
| Mechanical | {N} | {N} | {%} | |
| Firmware | {N} | {N} | {%} | |
| **Overall** | | | **{%}** | |

## Defects Found

| ID | Class | FR Ref | Symptom | Root Cause | Owner | Status |
|----|-------|--------|---------|------------|-------|--------|
| EVT-001 | Class 1 | FR-003 | ... | ... | {name} | Open |

## Exit Recommendation

{Proceed to DVT / Blocked — fix {N} Class 1 defects first}
```

### Step 3: DVT — Design Validation Test

**Objective:** Verify the design meets all reliability, environmental, and regulatory requirements.

**Entry criteria:**
- [ ] EVT exit approved (all Class 1 closed, pass rate ≥ 80%)
- [ ] DVT prototype units built (20-50 units, production-intent tooling where possible)
- [ ] External test lab scheduled (if needed for EMC/safety)

**Test scope:**
- Full functional re-test after EVT fixes
- Environmental (temperature cycling, humidity, vibration per technical_spec.md)
- EMC/EMI (conducted/radiated emissions, immunity)
- Safety (insulation, ground bond, leakage current)
- Reliability (accelerated life test, HALT if applicable)
- MTBF demonstration (if required by NFR)

**Exit criteria:**
- [ ] Overall pass rate ≥ 90%
- [ ] No Class 1 defects open
- [ ] All Class 2 defects have documented fix plan with owner + ETA
- [ ] DVT defect list with root cause for every failure

Write `artifacts/phase_4/dvt_report.md` (same structure as EVT report, with reliability/environmental/EMC categories added).

### Step 4: PVT — Production Validation Test

**Objective:** Validate that the production line can build the product at target yield and cycle time.

**Entry criteria:**
- [ ] DVT exit approved (pass rate ≥ 90%, no Class 1)
- [ ] Production tooling complete
- [ ] Production line set up with SOPs
- [ ] Operators trained
- [ ] PVT build quantity agreed (100-200 units)

**Test scope:**
- Production-line functional test (same as EVT/DVT but run by operators, not engineers)
- First-pass yield (FPY) — units passing without rework
- Cycle time per station
- Process capability (Cpk for critical dimensions/parameters)
- Out-of-box audit (random sample unboxed and tested as customer would)

**Exit criteria:**
- [ ] Overall yield ≥ target (set per product; typically ≥ 90%)
- [ ] First-pass yield ≥ 80%
- [ ] No Class 1 defects attributable to production process
- [ ] Cycle time within takt time target
- [ ] Cpk ≥ 1.33 for critical parameters

Write `artifacts/phase_4/pvt_report.md` (add yield, FPY, Cpk, cycle time tables).

### Step 5: Track Defect Trends

After each phase, update `artifacts/phase_4/defect_trend.md`:

```markdown
# Defect Trend: {project_name}

## Phase-over-Phase Comparison

| Phase | Units Tested | Total Defects | Class 1 | Class 2 | Class 3 | Pass Rate |
|-------|-------------|---------------|---------|---------|---------|-----------|
| EVT | {N} | {N} | {N} | {N} | {N} | {%} |
| DVT | {N} | {N} | {N} | {N} | {N} | {%} |
| PVT | {N} | {N} | {N} | {N} | {N} | {%} |

## Top Recurring Defects

| Defect | EVT Count | DVT Count | PVT Count | Trend | Root Cause Status |
|--------|-----------|-----------|-----------|-------|-------------------|
| {defect} | {N} | {N} | {N} | ↑/↓/→ | {resolved/open} |
```

### Step 6: Validation Freeze

After PVT passes all exit criteria, write `artifacts/phase_4/validation_freeze.md`:

```markdown
# Validation Freeze Sign-Off: {project_name}

**Date:** {date}

## Phase Results

| Phase | Pass Rate | Class 1 Open | Status |
|-------|-----------|-------------|--------|
| EVT | {%} | {N} | Pass / Fail |
| DVT | {%} | {N} | Pass / Fail |
| PVT | {%} | {N} | Pass / Fail |

## Confirmation

- [ ] All Class 1 defects closed across all phases
- [ ] All Class 2 defects have documented fix plan
- [ ] PVT yield ≥ target
- [ ] Defect trend shows improving trajectory (total defects ↓ phase-over-phase)
- [ ] Certification status reviewed (from hw-pm-cert if run in parallel)
```

Present the validation freeze to the user. Do not proceed to NPI without explicit confirmation.

## Output Format

Write all files to `artifacts/phase_4/`:

| File | Content | Required |
|------|---------|----------|
| `test_plan.md` | Full test plan with spec → test → criteria mapping | Yes |
| `evt_report.md` | EVT results with defect list and exit recommendation | Yes |
| `dvt_report.md` | DVT results with reliability/environmental/EMC data | Yes |
| `pvt_report.md` | PVT results with yield, FPY, Cpk, cycle time | Yes |
| `defect_trend.md` | Defect counts by phase and class, trend analysis | Yes |
| `validation_freeze.md` | Final sign-off with all-phase summary and confirmation | Yes |

## Hard Gate Checklist

```
[ ] Test Plan created, covering all PRD specs (every FR + NFR mapped to test method)
[ ] EVT exit: pass rate >= 80%, no Class 1 defects open
[ ] DVT exit: pass rate >= 90%, no Class 1 defects open, Class 2 have fix plans
[ ] PVT exit: yield >= target, first-pass yield >= 80%, Cpk ≥ 1.33
[ ] Defect trend shows improving trajectory (new defects ↓ each phase)
[ ] Validation freeze signed off by test lead
[ ] **Limiting Factor identified:** What is the single test or issue that gates the next milestone?
[ ] User confirmed, phase_status updated to "manufacturing"
```

## Questions to Ask Your Domain Experts

Before signing off on each validation phase exit, ask these questions:

### For Your Test Lead (at EVT exit)
- "Which failure surprised you the most — the one you didn't see coming?"
- "If you could only run 3 more tests before DVT, which 3 would tell us the most?"
- "What's the single worst measurement in the batch, and does it look like a fluke or a trend?"

### For Your Mechanical Engineer (at DVT entry)
- "Which tolerance stack is closest to causing a reliability failure?"
- "At production volumes, what process variation will degrade your worst-case result?"
- "If we had to reduce one test's sample size to hit schedule, which is the safest to trim?"

### For Your Electrical Engineer (at DVT exit)
- "What EMC failure mode are you most worried about at scale?"
- "If the power supply IC goes EOL, how much of the DVT data is invalidated?"
- "Which voltage rail has the least margin in the worst-case corner?"

### For Your Manufacturing Lead (at PVT planning)
- "What station on the line has the most operator-dependent quality variance?"
- "If first-pass yield is 10 points below target, what's your first corrective action?"
- "Which supplier's quality variation is most likely to appear only at volume?"

### For Yourself (at every phase exit)
- "Am I signing off because I believe the data, or because the schedule demands it?"

These questions do not replace formal test criteria. They supplement them with PM judgment.

## Dependencies

This skill reads from:
- `artifacts/phase_2/prd.md` — functional + non-functional requirements
- `artifacts/phase_2/technical_spec.md` — performance targets, environmental, reliability specs
- `artifacts/phase_3/design_freeze.md` — design freeze status, open P0/P1 issues from design

## Common Mistakes

- **Not creating a Test Plan before testing begins** — testing without mapped criteria produces unverifiable results
- **EVT exit without verifying all blocking issues closed** — Class 1 defects in EVT become Class 1 defects in PVT at 10x the cost
- **Rushing to PVT with known DVT failures** — each prototype revision costs time and money; fix at the right phase
- **No defect classification** — all failures look equally important without Class 1/2/3 severity; prioritize safety and functionality first
- **Ignoring defect trends** — if defect count is flat or rising phase-over-phase, the design is not converging. Stop and re-assess before PVT.
- **Skipping user confirmation between phases** — each phase exit is a commitment point. User must explicitly approve before moving to the next.
