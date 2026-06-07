---
name: hw-pm-cost
description: "Use throughout Phase 2-5 to manage BOM cost targets, should-cost analysis, and cost-down roadmap."
---

# Cost Management (hw-pm-cost)

## Overview
Runs across all phases — from target costing at spec phase through cost-down after launch. Maintains a living BOM with cost targets per line item.

## When to Use
- Any phase after Phase 1 Go decision
- BOM cost needs tracking against target margin
- Cost-down opportunity identified

**Don't use when:**
- Still in Phase 1 research (use hw-pm-research)
- Project is software-only with no BOM

## Process
1. Load target gross margin from `product_line.yaml`
2. Calculate target BOM cost from target ASP and margin
3. During design: read BOM from `business_case.md`, build `bom.md` with target vs estimated vs actual per line item
4. Perform should-cost analysis for high-value items (top 80% of BOM cost)
5. Track cost by phase: design-phase estimate → prototype actual → NPI quote → production price
6. Build `cost_roadmap.md`: versions × target price × timeline × approach (substitution/negotiation/redesign)
7. Update after each phase milestone

## Output Format
Write to `artifacts/shared/`:
- `bom.md` — BOM with cost breakdown
- `should_cost.md` — should-cost analysis per line item
- `cost_roadmap.md` — cost-down roadmap

## Hard Gate Checklist
```
[ ] BOM loaded from latest phase data
[ ] Should-cost analysis done for top 80% cost items
[ ] Cost roadmap exists with >= 2 cost-down levers
[ ] Current BOM cost vs target margin gap documented
```

## Common Mistakes
- Only tracking BOM cost at end of project (too late to influence)
- Not separating NRE (one-time) from unit cost (recurring)
- Ignoring MOQ impact on unit cost
