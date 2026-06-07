---
name: hw-pm-launch
description: "Use when manufacturing is ready to manage product launch, channel strategy, support readiness, and post-launch review."
---

# Product Launch Management (hw-pm-launch)

## Overview

This skill manages the final go-to-market phase after NPI and manufacturing are complete. It covers pricing finalization, channel allocation, support infrastructure setup, and post-launch performance review cadence.

`hw-pm-launch` answers: *"Is the product ready for market introduction?"*
It then tracks: *"Is the product performing as expected post-launch?"*

## When to Use

- NPI is complete and phase_status is "launch"
- Product is ready for market introduction
- You need to plan channel strategy and inventory allocation
- Support infrastructure needs to be set up before launch day

**Don't use when:**
- Manufacturing ramp is still in progress (go back to NPI phase)
- The product is still in development or gate review
- You only need pricing analysis (use `hw-pm-research` — Business Analyst agent)

## Position in the Skill System

This skill is a **post-gate** phase, executed after Gate 1 approval and after manufacturing/NPI completion. It does not feed back into the investment decision — it executes the go-to-market plan.

```dot
digraph launch_phase {
    rankdir=LR;
    node [shape=box, style=rounded];

    gate [label="hw-pm-gate\n(Go decision)"];
    npi [label="NPI /\nManufacturing"];
    launch [label="hw-pm-launch\n(Phase 5)", style=bold, color=blue];
    pfr [label="Post-Launch\nReview"];

    gate -> npi [label="Go → develop"];
    npi -> launch [label="NPI complete"];
    launch -> pfr [label="launched"];
}
```

## Process

### Step 1: Finalize Launch Pricing

Before any channel or support planning, confirm the final pricing structure:

| Element | Description | Data Source |
|---------|-------------|-------------|
| **MSRP** | Manufacturer's Suggested Retail Price | Business case ASP, competitive analysis |
| **Channel Margin** | Distributor/reseller discount off MSRP | Industry standard (typically 15-40%) |
| **Promotional Pricing** | Launch-period discounts, bundling | Marketing budget, competitive positioning |
| **Volume Tiers** | Price breaks at order quantity thresholds | Channel partner agreements |

Compare final MSRP against the target gross margin from `product_line.yaml`. If margin falls below threshold, flag for user decision.

### Step 2: Create Launch Plan

Write `launch_plan.md` covering:

```
## Launch Timeline
| Milestone | Date | Owner | Dependencies |
|-----------|------|-------|--------------|
| {milestone} | {date} | {name/role} | {dependency} |

## Critical Path
- {activity} → {activity} → {activity} (identify the longest chain)

## Owners & Responsibilities
| Role | Responsibility |
|------|---------------|
| {role} | {responsibility} |

## Launch Checklist
[ ] Press release / media kit ready
[ ] Product page live on website
[ ] Channel partners stocked
[ ] Sales team trained
[ ] Support team trained
[ ] Regulatory certifications posted
[ ] User documentation published

## Contingency Plan
- {risk scenario} → {mitigation action} → {owner}
```

### Step 3: Define Channel Strategy

Write `channel_strategy.md` covering distribution channels, inventory allocation, and lead times:

```
## Distribution Channels
| Channel | Type | Allocation % | Lead Time (days) |
|---------|------|-------------|------------------|
| {channel} | Direct / Distributor / Retail / OEM | {%} | {days} |

## Inventory Allocation
- Total launch quantity: {units}
- Safety stock: {units} ({%} of total)
- Channel buffer: {units} ({%} of total)

## Channel Partner Requirements
- Minimum order quantity: {units}
- Payment terms: {net days}
- Return policy: {terms}

## Logistics
- Shipping method: {method}
- Incoterms: {terms}
- Fulfillment centers: {locations}
```

### Step 4: Set Up Support Infrastructure

Write `support_sla.md` covering after-sales support:

```
## Warranty Terms
- Duration: {months/years}
- Coverage: {what's included / excluded}
- Claim process: {steps}

## RMA流程 (Return Merchandise Authorization)
### Step 1: Customer submits RMA request
- Channel: {web / phone / email}
- Required info: {order number, serial number, issue description}
- SLA: {hours} to acknowledge

### Step 2: Diagnosis
- {remote diagnosis / return for inspection}
- SLA: {days} to complete

### Step 3: Resolution
- Options: {repair / replacement / credit / refund}
- Turnaround time: {days}
- Shipping: {who covers freight}

## Spare Parts Plan
| Part | Stock Level | Lead Time | Supplier |
|------|-------------|-----------|----------|
| {part} | {qty} | {days} | {supplier} |

## Support Tiers
| Tier | Audience | Channel | Hours | SLA |
|------|----------|---------|-------|-----|
| L1 | End users | Chat / Email / Phone | {hours} | {response time} |
| L2 | Channel partners | Dedicated line | {hours} | {response time} |
| L3 | Escalation | Engineering | {business hours} | {response time} |
```

### Step 5: Plan Post-Launch Review Cadence

After launch, track performance at scheduled intervals. Write `post_launch_review.md`:

```
## Review Schedule
| Review | Timing | Focus |
|--------|--------|-------|
| 30-day | Day 30 | Early quality issues, first impressions, channel fill rate |
| 60-day | Day 60 | Return rate trending, channel reorder rate, NPS |
| 90-day | Day 90 | Quarter-end performance, first revision candidates |
| 6-month | Month 6 | Product fit assessment, market share estimate |
| 12-month | Month 12 | Year-one P&L, full product review, EOL evaluation |

## Metrics to Track
| Metric | Target | Review Point |
|--------|--------|--------------|
| NPS | ≥ {score} | 60-day, 90-day |
| Return rate | ≤ {%} | 30-day, 60-day, 90-day |
| Channel sell-through | ≥ {%} of allocation | 30-day, 60-day |
| Support ticket volume | ≤ {qty}/month | 30-day, 60-day, 90-day |
| RMA turnaround time | ≤ {days} | 30-day, 60-day |
| ASP vs. planned | within {%} | 30-day, 90-day, 12-month |
| Revenue vs. forecast | ≥ {%} | 90-day, 12-month |

## Escalation Triggers
- Return rate > {threshold}% → immediate engineering review
- NPS < {threshold} → immediate support process review
- Channel sell-through < {threshold}% → channel strategy review
- Safety stock below {threshold} units → production acceleration trigger
```

### Step 6: User Confirms Launch Readiness

Present the four output documents and the hard gate checklist to the user. Do not proceed without explicit confirmation.

### Step 7: Post-Launch Tracking

After launch confirmation, return to `post_launch_review.md` at each scheduled review point. Update the document with actual vs. planned metrics. Flag escalations per the triggers defined in Step 5.

If significant course correction is needed (recall, redesign, or discontinuation), escalate through the hw-pm entry point for a new investment decision cycle.

## Output Format

Write to `artifacts/phase_5/`:

| File | Content |
|------|---------|
| `launch_plan.md` | Full launch timeline with milestones, owners, and dependencies |
| `channel_strategy.md` | Distribution channels, inventory allocation, lead times |
| `support_sla.md` | Warranty, RMA流程, spare parts plan, support tiers |
| `post_launch_review.md` | Review schedule, metrics, targets, and escalation triggers |

### Required Sections in Each File

All output files must follow the section templates defined in Steps 2-5 above. No file may contain empty sections — any section that cannot be filled must include an explicit note: "TBD — {owner/role} to provide by {date}".

## Hard Gate Checklist

ALL items must pass before launch:

```
[ ] Launch pricing finalized against target margin (from product_line.yaml)
[ ] Launch plan created with timeline and owners
[ ] Channel inventory allocation defined with safety stock
[ ] Support SLA and RMA流程 documented
[ ] Post-launch review cadence scheduled with metric targets
[ ] User confirmed launch readiness
```

Any FAIL → launch is not ready. Do not proceed until all items pass.

## Common Mistakes

**Launching without channel inventory buffer:** Allocating 100% of inventory to pre-orders with no safety stock. → Always reserve 10-20% buffer for channel replenishment and RMAs.

**Not having support infrastructure ready on day 1:** Customer calls launch day but RMA流程 is still "being drafted." → Support plan must be finalized before launch announcement.

**No post-launch review plan:** Product launches and the team moves to the next project without tracking performance. → The 30/60/90 day cadence catches issues early while course correction is still cheap.

**Single-channel dependency:** All inventory with one distributor. → If that partner underperforms, the entire launch fails. Diversify across ≥2 channels where possible.

**Ignoring RMA costs in margin:** Launch pricing that doesn't account for expected warranty claims and return handling. → Factor 2-5% of revenue for RMA costs into the margin calculation.

**Missing escalation triggers:** A problem is visible in 30-day data but nobody notices until the quarterly review. → Define numeric triggers that automatically flag when action is needed.
