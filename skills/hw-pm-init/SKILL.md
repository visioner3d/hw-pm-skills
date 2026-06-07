---
name: hw-pm-init
description: Use when starting a new hardware product project from scratch — creates directory structure and config templates interactively, then routes to hw-pm-spec.
---

# Quick Project Initialization (hw-pm-init)

## When to Use

- **New product assessment** — product idea exists but no project directory or config files
- **Routed here by `hw-pm`** — state machine detects `project.yaml` is missing and dispatches init
- **Re-initialization** — user wants to restart a project from scratch

**Don't use when:**
- `project.yaml` already exists (hw-pm-spec handles this)
- You only need to update a single config field (edit the YAML directly)
- The task is Phase 1 research (use hw-pm-research)

## Process Overview

```
1. Project Identity → determine project name
2. Create Directory Structure
3. Generate Config Templates (full schema + comments)
4. Interactive Field Filling (key fields first, defer optional ones)
5. Project Summary → show final state
6. Route → hw-pm (state machine detects project.yaml → hw-pm-spec)
```

## Step-by-Step

### Step 1: Project Identity

Ask the user for the project name. If already inferable from context or the working directory, confirm instead:

```
> Initializing a new hardware product project.
> Is the project name "{inferred_name}"?
> A) Yes, proceed with this name
> B) No, I'll provide a different name
```

Once confirmed, set `PROJECT_NAME` and `PROJECT_DIR` (default: `projects/{PROJECT_NAME}/`).

If user chooses to specify a custom directory, accept an absolute or relative path.

### Step 2: Directory Structure

Create the following structure under `PROJECT_DIR`:

```
{PROJECT_DIR}/
├── company.yaml                 # Company-level config
├── product_line.yaml            # Product-line config
├── project.yaml                 # Project config (clarification log)
├── artifacts/
│   ├── phase_1_strategy/        # Research agent outputs
│   └── gate_reviews/            # Gate decision reports
```

Use `mkdir -p` for each path. Verify all directories exist before proceeding.

### Step 3: Generate Config Templates

Write three YAML files with **complete schema and YAML comments**. All fields are present. Key fields start empty (`""`, `0`, or `[]`). Non-key fields have sensible defaults.

All fields are annotated with:
- `# 必填` — must be filled before Phase 1 research can begin
- `# 建议填写` — strongly recommended for Phase 1 accuracy
- `# 可选` — has a sensible default, can be deferred

#### company.yaml Template

```yaml
# ──────────────────────────────────────────────
# Company-Level Configuration (company.yaml)
# ──────────────────────────────────────────────
company:
  name: ""                                   # 必填：公司名称

  strategy_points:                           # 必填：公司战略要点（至少 1 条）
    - ""                                     # 如: "成为行业领先的高精度测量方案提供商"

  industry: ""                               # 必填：所属行业（如: "Automotive, 3C, Industrial Metrology"）

  # ── 投资阈值 ──
  investment_thresholds:
    min_tam: 50000000                        # 建议填写：最小 TAM（$），默认 $50M
    min_npv: 10000                           # 可选：最小 NPV（$），默认 $10K
    min_irr: 0.12                            # 可选：最低 IRR，默认 12%
    min_gross_margin: 0.50                   # 可选：最低毛利率，默认 50%
    max_risk_exposure: 50                    # 可选：最大风险评分，默认 50

  # ── 财务参数 ──
  wacc: 0.12                                 # 可选：加权平均资本成本，默认 12%
  risk_free_rate: 0.05                       # 可选：无风险利率，默认 5%
```

#### product_line.yaml Template

```yaml
# ──────────────────────────────────────────────
# Product-Line Configuration (product_line.yaml)
# ──────────────────────────────────────────────
product_line:
  name: ""                                   # 必填：产品线名称（如: "3D Line Laser Scanners"）

  brand_positioning: ""                      # 必填：品牌定位描述（1-2 句话）

  target_gross_margin: 0.55                  # 建议填写：目标毛利率，默认 55%

  default_price_band:                        # 建议填写：目标价格区间 [$min, $max]
    min: 0                                   # 最低售价（$）
    max: 0                                   # 最高售价（$）

  key_competitors: []                        # 可选：主要竞争对手列表
```

#### project.yaml Template

```yaml
# ──────────────────────────────────────────────
# Project Configuration (project.yaml)
# ──────────────────────────────────────────────
project:
  project_name: "{PROJECT_NAME}"             # 必填：项目名称（与目录名一致）

  description: ""                            # 必填：一句话产品描述
                                             # 如: "High precision 3D line laser scanner
                                             #      for industrial quality inspection"

  clarification_log: []                      # 可选：澄清日志，由 hw-pm-clarify 写入
```

### Step 4: Interactive Field Filling

Ask the user questions **one at a time**. After each answer, update the corresponding YAML file in-place.

**Priority order — fill these first:**
1. `company.name` — "公司名称是什么？"
2. `company.strategy_points` — "公司的核心战略要点是什么？" (至少 1 条，可多条)
3. `company.industry` — "所属行业？" (如 "Automotive, 3C")
4. `product_line.name` — "产品线名称？"
5. `product_line.brand_positioning` — "品牌定位描述？"
6. `project.description` — "一句话描述这个产品？"

**Second pass — suggest defaults but confirm:**
7. `product_line.default_price_band` — "目标价格区间？如无则留空"
8. `product_line.key_competitors` — "已知的主要竞争对手？可留空，后续补充"
9. `company.investment_thresholds` — "投资阈值是否使用默认值？" (显示当前默认值)

**Deferred fields** — show once at end:
```
以下字段已填入默认值，可在后续步骤中随时更新：
- min_npv: $10K
- min_irr: 12%
- wacc: 12%
- target_gross_margin: 55%
是否调整？(A) 全部保留  (B) 选择调整
```

**Answer format:** Accept free-text input. For multi-value fields (strategy_points, competitors), accept newline-separated or comma-separated lists.

### Step 5: Project Summary

After all questions answered, display a summary:

```
┌─────────────────────────────────────────────┐
│  Project Initialization Complete             │
├─────────────────────────────────────────────┤
│  Project:       {PROJECT_NAME}               │
│  Company:       {company.name}               │
│  Industry:      {company.industry}            │
│  Product Line:  {product_line.name}           │
│  Description:   {project.description}         │
│  Price Band:    ${min} – ${max}               │
│  Competitors:   {count} listed                │
│                                              │
│  Files created:                               │
│    ✓ company.yaml                             │
│    ✓ product_line.yaml                        │
│    ✓ project.yaml                             │
│    ✓ artifacts/phase_1_strategy/              │
│    ✓ artifacts/gate_reviews/                  │
├─────────────────────────────────────────────┤
│  Next: Loading hw-pm to proceed to spec...    │
└─────────────────────────────────────────────┘
```

### Step 6: Route to hw-pm

Load the `hw-pm` skill. The state machine will detect `project.yaml` exists and route to `hw-pm-spec`.

```
Use the skill tool to load hw-pm
```

## What This Skill Does NOT Do

- **Define product requirements** — that is hw-pm-spec's job
- **Run research** — that is hw-pm-research's job
- **Edit existing config** — use manual YAML editing for updates
- **Validate business logic** — threshold values are accepted as-is

## Template Structure Rules for the Agent

When writing template files, follow these conventions:

1. **Every file** starts with a banner comment: `# ──────────────────── ...`
2. **Key fields** use `""`, `0`, or `[]` as placeholders
3. **Default fields** show the default value explicitly
4. **Comments** explain the field's purpose and show example values
5. **Grouping comments** (`# ── Section ──`) separate logical sections
6. **Unused keys are left in** — removing them would lose schema documentation
7. **Replace placeholder values in-place** when user provides a value — do not rewrite the entire file

Correct field-replacement example:
```
# Before (template):
  name: ""                            # 必填：公司名称

# After (user provides "LTS Precision Instruments"):
  name: "LTS Precision Instruments"   # 必填：公司名称
```

## Common Mistakes

**Skipping directory creation.** Agent tries to write files before verifying `mkdir -p`. → Always create dirs in Step 2 before writing any file.

**Overwriting existing project.** User already has `company.yaml` and runs init again. → Check for existing files first; ask user before overwriting.

**Too many questions at once.** User gets overwhelmed. → One question per message. Prefer multiple-choice when reasonable, but accept free-text answers.

**Rigid defaults.** User's industry may have very different norms. → Always show defaults but allow changing them.

**Writing template values as final output.** Template has `""` placeholders, agent forgets to ask user. → Step 4 completes ALL key questions before routing. Defer only optional fields.
