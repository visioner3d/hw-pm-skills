---
name: hw-pm-report
description: "Use after project phases complete to consolidate granular artifacts into human-readable reports. Always asks for report depth before generating."
---

# Report Consolidation (hw-pm-report)

## Overview

The hw-pm system produces 30+ granular artifact files across Phase 1-5. These are designed for agent-to-agent data exchange, not for human consumption. This skill consolidates them into integrated reports at the depth appropriate for the audience.

**Principle:** Read all artifacts. Never modify originals. Write to `artifacts/reports/`.

## When to Use

- **End of project** — all phases complete, need executive summary
- **Phase gate** — before presenting to stakeholders at any gate
- **Management review** — condensed view for non-PM audience
- **On demand** — user loads this skill explicitly

**Don't use when:**
- You are mid-research (interim reports waste time)
- User needs to edit raw artifacts (use the original skill instead)

## Report Depth Levels

| Depth | Output Files | Audience | Content Density |
|-------|-------------|----------|-----------------|
| **摘要** | 1 file: `executive_summary.md` | C-level, investors | 3-5 lines per phase, decision summary, Go/No-Go result |
| **标准** | 1 file: `master_report.md` | Management, PM | Key data tables, decision tree, metric trends, risk summary |
| **详细** | 4-5 files: `phase_{1..5}_report.md` | Engineering, QA | All critical data, per-phase drill-down, full decision trace |

## Depth Selection Protocol

**ALWAYS ask the user before generating.** Show the options:

```
> 报告深度：
> A) 摘要 — 1 份执行摘要，每阶段 3-5 行 + 决策结果
> B) 标准 — 1 份综合报告，含核心数据表、决策树、风险总览
> C) 详细 — 每 phase 1 份报告，含完整数据追溯（共 4-5 份）
```

Do not default to any option. Wait for user input.

## Process

### Step 1: Scan All Artifacts

Build an inventory of all existing artifact files:

```
artifacts/inventory:
  phase_1:
    - strategy_alignment.md
    - competitive_analysis.md / .json
    - user_research.md / .json
    - business_case.md / .json
    - discussion.md
    - gate_reviews/gate_1_review.md
    - audit_report.md (optional)
  phase_2:
    - prd.md / .json
    - technical_spec.md
    - feature_matrix.md
  phase_3:
    - design_review_log.md
    - design_issues.md
    - design_freeze.md
    - dfm_report.md
  phase_4:
    - test_plan.md
    - evt_report.md / dvt_report.md / pvt_report.md
    - defect_trend.md
    - validation_freeze.md
    - cert_matrix.md / cert_schedule.md / cert_compliance.md (optional)
  phase_5:
    - npi_checklist.md
    - supplier_status.md
    - pilot_run_report.md
    - ramp_plan.md
    - launch_plan.md
    - channel_strategy.md
    - support_sla.md
    - post_launch_review.md
  shared:
    - bom.md / should_cost.md / cost_roadmap.md
    - risk_register.md / issue_tracker.md / eco_log.md
```

If no artifacts exist, notify: "No project artifacts found. Run hw-pm-init first."

### Step 2: Ask for Depth

Present the three depth options (above). Do not skip this step.

### Step 3: Generate Reports

Create `artifacts/reports/` directory. Generate files based on selected depth.

#### 摘要 Mode: `executive_summary.md`

```markdown
# Executive Summary: {project_name}

**Generated:** {date}
**Phase Status:** {phase_status}
**Investment Decision:** {Go/No-Go}

## Phase 1 — Investment Decision
{3-5 lines: Market assessment, financial viability, strategic fit, gate result}

## Phase 2 — Product Definition
{3-5 lines: PRD scope, key features, target spec highlights}

## Phase 3 — Design
{3-5 lines: Design freeze status, key issues resolved, DFM findings}

## Phase 4 — Validation
{3-5 lines: EVT/DVT/PVT pass rates, certification status, defect trend direction}

## Phase 5 — Manufacturing & Launch
{3-5 lines: NPI readiness, ramp progress, launch status}

## Key Risks (Top 3)
| Rank | Risk | Status | Owner |
|------|------|--------|-------|

## Financial Summary
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| {metric} | {target} | {actual} | ✓/✗ |
```

Extract content by reading each artifact and condensing to key conclusions. Do not copy raw data — synthesize.

#### 标准 Mode: `master_report.md`

```markdown
# Master Report: {project_name}

**Generated:** {date}
**Phase Status:** {phase_status}

## 1. Project Overview
{project_name}, {description}, {industry}

## 2. Decision Tree
```
Phase 1: {Go/No-Go} (confidence: {level})
Phase 2: PRD {approved/rejected}
Phase 3: Design {frozen/rework}
Phase 4: Validation {passed/conditional}
Phase 5: Launch {live/delayed}
```

## 3. Key Metrics (per phase table)
| Metric | Phase 1 | Phase 2 | Phase 3 | Phase 4 | Phase 5 |
|--------|---------|---------|---------|---------|---------|
| TAM/SOM | {value} | — | — | — | — |
| Target ASP | {value} | {value} | {value} | {value} | {final} |
| BOM Cost | {est} | {target} | {est} | {actual} | {final} |
| Gross Margin | {est} | {target} | {target} | {actual} | {final} |

## 4. Risk Summary
Read risk_register.md, extract top risks by score.

## 5. Timeline
| Phase | Start | End | Duration |
|-------|-------|-----|----------|

## 6. Gate Results
Phase 1: {Go/No-Go} — {rationale}
(Phase 2-5 don't have formal gates, list completion status)
```

#### 详细 Mode: `phase_{1..5}_report.md`

One report per phase that exists. Structure:

```markdown
# Phase {N} Report: {phase_name}

## Summary
{3-5 line phase summary}

## Input Artifacts
- {file1} — {key data extracted}
- {file2} — {key data extracted}
...

## Key Findings
### {Finding 1}
{Detail from artifacts}

### {Finding 2}
{Detail from artifacts}

## Decisions Made
| Decision | Rationale | Confidence |
|----------|-----------|------------|
| {decision} | {why} | {level} |

## Data Table
{Copy key structured data from phase artifacts, formatted as tables}

## Open Items (if any)
| Item | Owner | ETA |
|------|-------|-----|
```

Read every artifact in the phase directory and the shared directory. Include all critical data but omit raw logs, verbose comments, and intermediate calculations.

## Report File Naming Convention

```
artifacts/reports/
├── executive_summary.md     (摘要 mode)
├── master_report.md         (标准 mode)
├── phase_1_report.md        (详细 mode)
├── phase_2_report.md
├── phase_3_report.md
├── phase_4_report.md
└── phase_5_report.md
```

If a report file already exists, ask before overwriting:
```
报告文件已存在: artifacts/reports/{filename}
(A) 覆盖
(B) 保留原文件，以新时间戳另存
(C) 跳过
```

## Hard Gate Checklist

```
[ ] User selected report depth (never default)
[ ] All existing artifact files scanned
[ ] Reports written to artifacts/reports/
[ ] Original artifacts untouched
[ ] User confirmed report content
```

## Common Mistakes

**Generating without user input.** "I'll just make a summary since it's faster." → Always ask. The user knows the audience.

**Copying raw artifact content.** Reports should synthesize, not concatenate. If the PRD is 20 pages, the phase report isn't 20 pages — it's key decisions and specs.

**Assuming EOL completeness.** User may call this skill mid-project for a stakeholder review. Only include phases that have complete artifacts.

**Forgetting shared artifacts.** cost_roadmap.md and risk_register.md span all phases. Include them in every phase report in detailed mode.
