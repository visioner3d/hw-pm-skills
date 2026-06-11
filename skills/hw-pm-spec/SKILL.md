---
name: hw-pm-spec
description: "Use when defining product requirements, investment thresholds, and configuration at the start of a hardware product initiative, or when company/product-line configuration needs to be established."
---

# Spec-Driven Development for Hardware PM (hw-pm-spec)

## Overview

Spec-Driven Development (SDD) in hardware product management means the **spec IS the investment thesis**. Every requirement is a hypothesis to be tested by research; every threshold is a gate criterion to be passed. The spec defines what "good enough to invest" looks like before any research begins.

This skill establishes the three-level configuration system and the spec structure that all downstream skills depend on.

## When to Use

- **New project** — "Evaluate this product idea"
- **Company config setup** — first-time strategy and threshold definition
- **Product line definition** — brand positioning, target margin, default competitors
- **Threshold adjustment** — investment criteria need updating

**Don't use when:**
- Research is already underway (go back and spec first — the hard gate)
- You only need to clarify one ambiguous spec field (use `hw-pm-clarify`)

## Three-Level Configuration

```
┌─────────────────────────────────┐
│  company.yaml                   │  ← shared across ALL projects
│  ├── strategy_points            │
│  ├── default_investment_limits  │
│  ├── industry / compliance      │
│  └── wacc / risk_free_rate      │
├─────────────────────────────────┤
│  product_line.yaml              │  ← shared within ONE product line
│  ├── brand_positioning          │
│  ├── target_gross_margin        │
│  ├── default_price_band         │
│  └── key_competitors            │
├─────────────────────────────────┤
│  project.yaml                   │  ← per-project, minimal
│  ├── project_name               │
│  ├── one_line_description       │
│  └── (overrides if needed)      │
└─────────────────────────────────┘
```

### Merge Rules

1. Load `company.yaml` → base layer
2. Load `product_line.yaml` → merge (project-specific fields override company)
3. Load `project.yaml` → merge (project overrides both)
4. All configs are shallow-merged at top-level keys

### Required Fields in Company Config

```yaml
company:
  strategy_points: [str1, str2, ...]
  investment_thresholds:
    min_tam: <number>        # e.g. 50000000
    min_npv: <number>
    min_irr: <number>        # e.g. 0.12
    min_gross_margin: <number>  # e.g. 0.50
    max_risk_exposure: <number> # optional
  industry: <string>
```

### Required Fields in Product Line Config

```yaml
product_line:
  brand_positioning: <string>
  target_gross_margin: <number>
  default_price_band: [min, max]
  key_competitors: [name1, name2]
```

### Required Fields in Project Config

```yaml
project:
  project_name: <string>
  description: <string>  # one-line description is the seed for ALL research
```

## Spec Structure

A valid spec must define:

1. **Product one-line description** — the seed for all agent research
2. **Company strategy points** — inherited from company.yaml
3. **Investment thresholds** — inherited with optional project override
4. **Evaluation dimensions** — ≥3 of the 5 standard dimensions:
   - Market attractiveness (TAM ≥ min_tam)
   - Financial health (NPV > 0, IRR > risk-free + premium)
   - Gross margin (≥ min_gross_margin)
   - Risk exposure (< max_risk_exposure)
   - Strategic fit (1-5 subjective)

## Output Format

The spec is a valid `project.yaml` with all config merged:

```yaml
project:
  project_name: L20
  description: "High precision 3D line laser scanner"
company:
  strategy_points: ["Become the best provider of high-precision 3D line laser scanners in the world"]
  investment_thresholds:
    min_tam: 50000000
    min_npv: 10000
    min_irr: 0.12
    min_gross_margin: 0.50
  industry: "Automotive, 3C"
product_line:
  brand_positioning: "Premium precision measurement"
  default_price_band: [200, 600]
  key_competitors: ["SSZN"]
```

## Hard Gate Checklist

ALL items must pass before leaving this skill:

**Minimal spec (sufficient to start research):**
```
[ ] project.yaml exists with a concrete, unambiguous product description
[ ] company.strategy_points has at least 1 entry
[ ] company.industry is set
[ ] investment_thresholds exist (default values from company.yaml template are acceptable)
[ ] Minimum 3 evaluation dimensions defined (default: Market, Financial, Margin, Risk, Strategic)
```

**Full spec (if PM wants to pre-configure before research — optional):**
```
[ ] product_line.default_price_band defined (otherwise auto-inferred by Market Analyst)
[ ] product_line.key_competitors defined (otherwise auto-inferred by Market Analyst)
[ ] company.name set (otherwise inferred from project context)
```

Either state is valid. Research agents will fill gaps with auto-inferred data and mark those data points with `confidence: medium`.
Any FAIL on minimal spec → the spec is incomplete. Do not proceed to `hw-pm-research`.

## Template System

Default templates are inline below. Users may override by placing a file with the same name in their project config directory.

```yaml
# company.yaml default template (same as hw-pm-init canonical defaults)
company:
  strategy_points: []
  investment_thresholds:
    min_tam: 50000000
    min_npv: 10000
    min_irr: 0.12
    min_gross_margin: 0.50
  industry: ""
```

```yaml
# project.yaml default template
project:
  project_name: ""
  description: ""
```

## Common Mistakes

**Empty spec:** One-line description too vague ("a better device") → agents produce unusable output. Spend time on a concrete description with context.

**Copy-paste thresholds:** Using wrong thresholds for a different industry/category. Always verify min_tam and min_margin match the product category.

**Missing overrides:** Price band in product_line.yaml is $200-$600 but project needs $1000+. Project.yaml override exists for this purpose.

**Skipping the hard gate:** "I'll define the spec more later" → downstream skills will fail because thresholds are missing. Complete the spec first.

**Bypassing init:** Using hw-pm-spec directly without hw-pm-init. → hw-pm-spec checks for config file existence in its hard gate. If files are missing, route back through hw-pm → hw-pm-init first.
