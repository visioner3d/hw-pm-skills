---
name: hw-pm-npi
description: "Use after PVT to manage manufacturing ramp, supplier readiness, pilot runs, and production launch."
---

# New Product Introduction (hw-pm-npi)

## Overview

Manages the transition from engineering to manufacturing. Ensures factory, suppliers, tooling, and materials are ready for volume production.

This skill coordinates the NPI phase — from PVT completion through ramp to launch — covering readiness verification, pilot execution, yield analysis, and supplier qualification.

## When to Use

- **PVT complete** — `phase_status` is `"manufacturing"` or next phase is launch
- **Pre-ramp readiness** — need to check factory, tooling, and materials before committing to volume
- **Supplier qualification** — assessing supply chain readiness for peak production
- **Pilot production** — planning and executing a controlled pilot run before full ramp
- **Ramp planning** — creating weekly volume targets, line allocation, and headcount plan

**Don't use when:**
- Still in EVT/DVT (use `hw-pm-prototype` or iterate with engineering)
- Full production is already stabilized (use standard operations)
- You only need to clarify one ambiguous spec or config field (use `hw-pm-clarify`)

## Phase Status Model

This skill operates during the **manufacturing** phase. After user confirmation, `phase_status` advances to `"launch"`.

```
┌──────────┐     ┌──────────────┐     ┌───────────┐
│    PVT   │  →  │ Manufacturing│  →  │  Launch   │
│ (verify) │     │  (this skill)│     │ (shipping)│
└──────────┘     └──────────────┘     └───────────┘
```

## NPI Readiness Checklist

Create the following checklist in `artifacts/phase_5/npi/npi_checklist.md`. Every item must be assessed and any critical blocker resolved before pilot run.

### Materials
- [ ] **BOM fully released** — all part numbers assigned and frozen
- [ ] **Long-lead materials on order** — lead times verified against ramp schedule
- [ ] **Safety stock policy defined** — buffer levels for critical components
- [ ] **Supplier delivery schedules confirmed** — PO schedule matches ramp curve

### Tooling & Equipment
- [ ] **Production tooling qualified** — mold/tool trials complete and signed off
- [ ] **Test fixtures built and validated** — pass/fail correlation with engineering
- [ ] **Burn-in / aging racks ready** — capacity matches target throughput
- [ ] **Packaging line set up** — box, label, insert specs verified

### Factory & Line
- [ ] **Production line layout approved** — stations, WIP flow, material lanes
- [ ] **SOPs posted at each station** — work instructions, torque values, visual aids
- [ ] **Line balance validated** — Takt time meets target cycle time
- [ ] **ESD / safety audits passed** — wrist straps, mats, grounding, PPE

### Programs & Systems
- [ ] **ERP BOM loaded** — correct routing, lead time, and cost roll-up
- [ ] **MES / traceability configured** — serial tracking, failure logging
- [ ] **QC inspection plan active** — AQL levels, sampling frequency, hold procedure
- [ ] **Repair / RMA workflow tested** — return flow for field failures

### People & Training
- [ ] **Line operators trained and certified** — signed training matrix
- [ ] **QC inspectors trained** — measurement techniques, defect criteria
- [ ] **Production supervisors appointed** — shift coverage, escalation path
- [ ] **Service / support team briefed** — known failure modes, replacement flow

## Supplier Readiness

For each key part or assembly (especially sole-source and long-lead items), assess and write to `artifacts/phase_5/npi/supplier_status.md`:

| Part / Module | Supplier | Lead Time | Capacity vs Peak | QC Status | Risk | Mitigation |
|---|---|---|---|---|---|---|
| Custom ASIC | Acme Semi | 16 wks | 80% of peak | PPAP signed | Amber | Place 2x buffer order |

### Assessment criteria
- **Lead time** — current confirmed lead time vs ramp schedule tolerance
- **Capacity vs peak** — maximum monthly output vs ramp peak volume
- **QC status** — incoming inspection results, PPAP/FAI sign-off
- **Risk** — Red (blocker), Amber (watch), Green (ready)

## Pilot Run

Plan and execute a controlled pilot of **100–500 units** (adjust for product complexity).

### Pilot objectives
1. Validate production-line yield matches engineering expectations
2. Confirm cycle time and line balance
3. Expose assembly and test fixture issues not caught in PVT
4. Train operators under realistic conditions
5. Generate first-article units for reliability sampling

### Pilot execution steps
1. Set pilot quantity and success criteria in `pilot_run_report.md`
2. Secure pilot materials — dedicated lot, not engineering samples
3. Staff line with trained operators + engineering support for first-run monitoring
4. Run pilot at target cycle time, tracking each station's output
5. Log all defects by station with root cause
6. Collect yield, first-pass yield (FPY), and defect Pareto

## Pilot Run Analysis

After pilot, write to `artifacts/phase_5/npi/pilot_run_report.md`. Include:

- **Lot summary** — date, quantity, stations used, shift hours
- **Overall yield** — pass / fail / rework counts
- **First-pass yield (FPY)** — units that passed without rework
- **Defect Pareto** — top defect types by frequency
- **Bottleneck analysis** — station with longest cycle time / highest WIP
- **Test coverage gaps** — failures that escaped to higher-level test
- **Corrective actions** — what must be fixed before ramp with owner and ETA

## Ramp Plan

Write `artifacts/phase_5/npi/ramp_plan.md` with weekly targets and resource requirements:

### Ramp curve (example)

| Week | Volume | Lines | Operators | Yield Target | Notes |
|---|---|---|---|---|---|
| W1 | 500 | 1 | 20 | >85% | Cautious ramp, iron out issues |
| W2 | 1000 | 1 | 35 | >90% | |
| W3 | 2000 | 2 | 60 | >93% | Add second line |
| W4 | 3000 | 2 | 85 | >95% | Peak steady-state |

### Resource planning
- **Headcount ramp** — hiring / training lead time for operators and QC
- **Material pipeline** — PO timing to avoid stockouts at peak
- **Capacity headroom** — line speed, test fixture utilization, burn-in slots
- **Contingency** — fallback plan if yield lags or supplier misses delivery

## Output Format

All artifacts go under `artifacts/phase_5/npi/`:

- `artifacts/phase_5/npi/npi_checklist.md` — NPI readiness items with status
- `artifacts/phase_5/npi/supplier_status.md` — supplier assessment per part
- `artifacts/phase_5/npi/pilot_run_report.md` — pilot results and analysis
- `artifacts/phase_5/npi/ramp_plan.md` — volume ramp schedule

## Hard Gate Checklist

Before user confirmation and phase transition to `"launch"`:

```
[ ] NPI checklist complete, all critical items green
[ ] Key suppliers assessed and ready
[ ] Pilot run completed with yield analysis
[ ] Ramp plan created with weekly targets
[ ] **Limiting Factor identified:** What single constraint (supplier, yield, capacity) gates the production ramp?
[ ] User confirmed, phase_status updated to "launch"
```

## Dependencies

This skill reads from:
- `artifacts/phase_4/` — PVT results (yield, open issues)
- `company.yaml` — target margin, cost constraints
- `project.yaml` — BOM, spec, volume targets

## Common Mistakes

- **Starting ramp before NPI checklist is complete** — missing SOPs or unqualified tooling cause massive yield loss at volume
- **Not validating supplier capacity for peak** — suppliers who qualify at low volume fail at 5x-10x peak
- **Underestimating training time** — operators need hands-on hours before hitting cycle time; first-pass yield drops sharply with new hires
- **Pilot run with no defect logging** — if you don't capture per-station defects, you can't prioritize corrective actions
- **Ramp plan with no contingency** — a single supplier miss or yield gap can stall the entire curve
