# hw-pm-skills 使用指南

> 通过一个完整实例（L20 高精度 3D 线激光扫描仪），演示从产品想法到投资决策的完整工作流。

## 实例场景

某精密仪器公司计划推出一款新产品 **L20**，定位为高精度 3D 线激光扫描仪，目标价格区间 $200–$600，主要竞争对手为 Keyence。公司战略是"成为全球最佳高精度 3D 线激光扫描仪提供商"。

---

## 第一步：安装与初始化

### 安装

```json
{
  "plugin": ["hw-pm-skills@git+https://github.com/visioner3d/hw-pm-skills.git"]
}
```

重启 OpenCode，加载入口技能：

```
Use the skill tool to load hw-pm
```

### 快速初始化（hw-pm-init 自动完成）

加载 `hw-pm` 后，状态机检测到 `project.yaml` 不存在，自动路由到 `hw-pm-init`。Agent 依次：

1. 确认项目名称 **L20**
2. 创建目录结构（`artifacts/phase_1_strategy/`、`artifacts/gate_reviews/`）
3. 生成三级配置模板（完整 schema + YAML 注释）
4. 交互式引导填写关键字段
5. 路由回 `hw-pm` → 进入下一阶段

> 如果已有配置，可以手动创建 `projects/L20/` 目录并放置空白 YAML 文件，`hw-pm` 会直接路由到 `hw-pm-spec`。

---

## 第二步：配置体系（hw-pm-spec）

加载 `hw-pm-spec` 技能，按三级配置结构定义产品需求。

### company.yaml

```yaml
company:
  name: "LTS Precision Instruments"
  strategy_points:
    - "Become the best provider of high-precision 3D line laser scanners in the world"
    - "Focus on industrial metrology and quality inspection markets"
  investment_thresholds:
    min_tam: 50000000        # $50M minimum TAM
    min_npv: 10000           # $10K minimum NPV
    min_irr: 0.12            # 12% minimum IRR
    min_gross_margin: 0.50   # 50% minimum gross margin
    max_risk_exposure: 50    # risk score cap
  industry: "Automotive, 3C, Industrial Metrology"
  wacc: 0.12                # Weighted Average Cost of Capital
  risk_free_rate: 0.05      # Risk-free rate for IRR premium calc
```

### product_line.yaml

```yaml
product_line:
  name: "3D Line Laser Scanners"
  brand_positioning: "Premium precision measurement for industrial applications"
  target_gross_margin: 0.55
  default_price_band: [200, 600]
  key_competitors: ["Keyence"]
```

### project.yaml

```yaml
project:
  project_name: "L20"
  description: "High precision 3D line laser scanner for industrial manufacturing quality inspection"
```

### 硬门禁检查

加载 `hw-pm-spec` 后，agent 自动执行预检：

- [x] **项目已初始化**（company.yaml / product_line.yaml / project.yaml 存在）
- [x] 产品一句话描述已明确
- [x] 公司配置已加载（company.yaml）
- [x] 产品线已选择（product_line.yaml）
- [x] 投资阈值已定义（min_tam, min_npv, min_irr, min_margin）
- [x] 评估维度 ≥ 3 个（市场、财务、毛利率、风险、战略）
- [x] 项目目录已创建

**全部通过** → 可以进入下一步。

---

## 第三步：澄清（hw-pm-clarify，可选）

加载 `hw-pm-clarify`，检查 spec 是否存在多义性：

```
检查发现：
1. 描述 "High precision 3D line laser scanner" → 无歧义 ✓
2. 价格区间 $200-$600 → 已定义 ✓
3. 竞品 Keyence → 已指定 ✓
4. 阈值与品类匹配？→ $50M TAM 阈值对于工业激光扫描仪是否合适？
```

**需要澄清的问题：**

> 公司默认 TAM 阈值是 $50M。对于工业 3D 线激光扫描仪这个细分市场，这个阈值是否合适？
> A) 合适，这个细分市场全球 TAM 约在 $50-$200M 之间
> B) 偏高，实际 TAM 约 $10-$50M，建议将阈值下调到 $10M
> C) 偏低，这是一个快速增长市场，TAM 应在 $200M+，阈值可以保持或上调

用户选择 **A**，确认 $50M 阈值合适。澄清日志写入 project.yaml：

```yaml
project:
  project_name: "L20"
  description: "High precision 3D line laser scanner for industrial manufacturing quality inspection"
clarification_log:
  - field: "investment_thresholds.min_tam"
    original: 50000000
    clarified: 50000000
    user_choice: "A) 合适，TAM 约 $50-$200M"
    timestamp: "2026-06-07T10:00:00Z"
```

无其他歧义 → 进入调研阶段。

---

## 第四步：并行调研（hw-pm-research）

加载 `hw-pm-research`。Agent 作为 Squad Lead，派发 4 个子 agent 并行执行。

### 派发指令示例

```
"Phase 1 spec is complete. Dispatching 4 research agents in parallel:
 1. Strategic Planner — strategy alignment
 2. Market Analyst — competitive analysis + market sizing
 3. User Researcher — personas + pain points
 4. Business Analyst — BOM + unit economics + NPV/IRR
All agents are independent and run concurrently."
```

### 各子 agent 接收到的 Prompt（简化）

**战略规划师：**
```
Role: Strategic Planner
Context:
- Project: L20
- Description: High precision 3D line laser scanner for industrial manufacturing
- Company strategy: Become the best provider of high-precision 3D line laser scanners
- Price band: $200-$600

Mandatory sections:
1. Strategic Fit Score (1-5)
2. Product Roadmap Impact
3. Portfolio Cannibalization Risk (table)
4. Strategic Risks (≥2)
```

**市场分析师：**
```
Role: Market Analyst
Tools: web_search, read_file
Context:
- Project: L20
- Description: High precision 3D line laser scanner
- Price band: $200-$600
- Key competitors: Keyence

Mandatory sections:
1. Competitive Analysis (≥3 competitors)
2. Market Sizing (TAM + SAM + SOM)
3. Market Trends (≥3)
```

**用户研究员：**
```
Role: User Researcher
Context:
- Project: L20
- Description: High precision 3D line laser scanner

Mandatory sections:
1. User Personas (≥2)
2. Pain Points (≥3)
3. Jobs-to-be-Done (≥3)
```

**商业分析师：**
```
Role: Business Analyst
Tools: financial_calc, read_file
Context:
- Project: L20
- Investment thresholds for reference

Mandatory sections:
1. Unit Economics Model
2. Bill of Materials (≥5 items)
3. Three-Year Financial Projection
4. NPV + IRR + Breakeven + Payback
```

### 产出物

4 个子 agent 返回后，产出以下 8 个文件：

```
artifacts/phase_1_strategy/
├── strategy_alignment.md
├── competitive_analysis.md
├── competitive_analysis.json
├── user_research.md
├── user_research.json
├── business_case.md
└── business_case.json
```

### 产出物示例

**战略规划师产出** (`strategy_alignment.md`)：

```markdown
# Strategic Alignment Analysis: L20

## Strategic Fit Score: 5 / 5
Direct alignment with company strategy — L20 is explicitly a high-precision 3D
line laser scanner, the core focus of the company.

## Portfolio Cannibalization Risk
| Product | Likelihood | Notes |
|---------|------------|-------|
| L15 (existing high-end) | High | Price/precision overlap; mitigate via tiering |
| L10 (entry-level) | Low | Different price segment |

## Strategic Risks
1. Execution risk: High precision requires flawless manufacturing
2. Over-specialization: If L20 fails, company's entire strategic bet is weakened
```

**市场分析师产出** (`competitive_analysis.json`)：

```json
{
  "tam": { "value": 52.5, "unit": "billion", "confidence": "medium", "source": "Statista Consumer Electronics Report 2025" },
  "competitors": [
    { "name": "Keyence", "price": 299, "confidence": "high", "source": "Amazon Keyence store" },
    { "name": "TechNovo", "price": 450, "confidence": "medium", "source": "TechNovo website" },
    { "name": "Globex", "price": 580, "confidence": "medium", "source": "Globex product page" }
  ]
}
```

**用户研究员产出** (`user_research.md`)：

```markdown
## Persona 1: Efficiency-Focused Office Manager
- Demographics: 35–50, corporate office, $65k–$95k
- Goals: Streamline operations, reduce overhead, integrate with existing software
- Frustrations: Frequent hardware failures, complex setup, cross-device issues
- Context: Shared office space, group tasks

## Persona 2: Field Technician
- Demographics: 28–45, field service, $55k–$85k
- Goals: Rugged performance, data capture on-site, cloud sync
- Frustrations: Overheating, dust ingress, fragile screens
```

**商业分析师产出** (`business_case.md`) ：

```markdown
## Unit Economics
| Metric | Value | Confidence |
|--------|-------|------------|
| ASP | $400 | Medium |
| BOM Cost | $140 | Medium |
| Gross Margin | 50.0% | Medium |

## Financial Metrics
| Metric | Value | Threshold | Status |
|--------|-------|-----------|--------|
| NPV (12%) | $3,890,218 | >$0 | Pass |
| IRR | 196% | >12% | Pass |
| Payback | 10 months | <3 years | Pass |
```

### 收集与初步综合

4 个 agent 返回后，Squad Lead 验证文件完备性，识别以下发现：

```
发现 1: 市场分析师定位 L20 为 "智能消费设备"，用户研究员定位为 "工业测量工具"
   → 定位矛盾，需要解决

发现 2: 商业分析师假定 $400 ASP，但竞品 Keyence 定价 $299
   → ASP 假设需要验证

发现 3: 战略匹配满分 (5/5)，但市场数据不完整
   → 置信度偏低
```

进入下一阶段。

---

## 第五步：5 层完备性审查（hw-pm-review）

加载 `hw-pm-review`，逐层执行审查。

### Layer 1: 流程合规

```
[x] project.yaml 存在且字段完整
[x] company.yaml 存在
[x] product_line.yaml 存在
[x] 4 个 agent 全部返回
[x] 无 crash/超时
```

### Layer 2: 产物完整

```
[x] strategy_alignment.md      存在 (341 字节)
[x] competitive_analysis.md    存在
[x] competitive_analysis.json  存在，JSON 有效
[x] user_research.md           存在
[x] user_research.json         存在，JSON 有效
[x] business_case.md           存在
[x] business_case.json         存在，JSON 有效
```

### Layer 3: 数据覆盖

```
战略:
[x] 战略匹配评分: 5/5
[x] 路线图影响分析
[x] 蚕食风险评估
[x] 风险 ≥ 2 条

市场:
[x] TAM: $52.5B (confidence: medium)
[x] SAM: $21B (confidence: medium)
[x] SOM: $315M (confidence: low)
[x] 竞品 ≥ 3 家
[x] 市场趋势 ≥ 3 条

用户:
[x] 用户画像 ≥ 2 个
[x] 痛点排行 ≥ 3 条
[x] JTBD ≥ 3 条

商业:
[x] BOM ≥ 5 个科目
[x] NPV + IRR 已计算
[x] 盈亏平衡 + 回收期
[x] 3 年财务预测
```

### Layer 4: 交叉一致

```
[x] 市场定价区间 ($200-$600) ↔ 商业 ASP ($400) — 一致
[!] 用户定位（工业技术人员）↔ 市场定位（智能消费设备）— 矛盾
     → 市场分析师将 L20 与消费级智能设备对标
     → 用户研究员描述的是工业测量场景
     → 严重程度: Blocking — 需要解决才能准确计算 BOM 和定价
[x] 竞品 feature set ↔ BOM allocation — 合理
[x] 置信度分布 — 合理，无系统性高估
```

**发现矛盾：定位矛盾（Layer 4, Blocking）**

这是一个 **Blocking 级矛盾**。参考矛盾解决协议：

1. **数据驱动** — 市场分析师引用的是消费电子报告（Statista），与 L20 的工业定位不匹配。置信度：Low
2. **阈值判决** — 无适用阈值
3. **呈报用户**

> L20 的定位存在矛盾：市场分析将其定位为消费智能设备，用户研究定位为工业测量工具。这两个方向需要不同的 BOM 分配和定价策略。你认为 L20 应该定位为：
> A) 工业精密测量仪器（竞争 accuracy 和 reliability）
> B) 智能消费设备（竞争 feature set 和 price）

用户选择 **A**（工业精密测量仪器）。

### Layer 5: 就绪度评估

```
Layer 1: 3/3 PASS
Layer 2: 7/7 PASS
Layer 3: 16/17 PASS (SOM confidence: low)
Layer 4: 矛盾已解决（用户确认定位 A）

判决: CONDITIONAL
条件:
- SOM 置信度为 low，门禁评审时需按 0.85× 折扣
- 定位已澄清为工业测量，市场分析中引用消费电子数据的部分置信度降级
```

### 产出 `discussion.md`

```markdown
# Phase 1 Synthesis: L20

## Executive Summary: CONDITIONAL

1/4 data gaps resolved. Positioning clarified as industrial measurement tool.
Proceed to gate with confidence discounting on market data.

## 1. Analysis Completeness & Confidence
| Analysis | Confidence | Quality | Key Strength | Key Weakness |
|----------|------------|---------|--------------|--------------|
| Strategic | High | Excellent | Perfect alignment | N/A |
| Market | Medium | Fair | TAM/SAM estimated | Used consumer data for industrial product |
| User | High | Good | Detailed industrial personas | Small sample implied |
| Business | Medium | Fair | NPV/IRR calculated | ASP assumption unvalidated |

## 2. Data Gaps
- SOM confidence low ($315M, estimated via industry average)
- ASP ($400) not validated against industrial buyers' WTP

## 3. Key Assumptions
| Assumption | Confidence | Risk if Wrong |
|------------|------------|---------------|
| $400 ASP achievable | Medium | Revenue model collapses |
| $140 BOM realistic | Medium | Margin erosion |
```

进入门禁评审。

---

## 第六步：投资决策门禁（hw-pm-gate）

加载 `hw-pm-gate`，按 5 维度评分。

### 评分过程

**维度 1: 市场吸引力 (25%)**
```
TAM = $52.5B ≥ $50M → PASS
Confidence: medium (数据来源为消费电子报告, 与工业定位部分不匹配)
折扣后得分: 1.0 × 0.85 = 0.85
```

**维度 2: 财务健康 (25%)**
```
NPV = $3,890,218 > $0 → PASS
IRR = 196% > 12% → PASS
Confidence: medium (ASP 未经验证)
折扣后得分: 1.0 × 0.85 = 0.85
```

**维度 3: 毛利率 (15%)**
```
Gross margin = 50.0% ≥ 50% → PASS
Confidence: medium (BOM 为估算)
折扣后得分: 1.0 × 0.85 = 0.85
```

**维度 4: 风险敞口 (15%)**
```
风险项: 制造精度风险(4×4=16), 定位偏差(3×3=9), BOM 超支(4×3=12)
总分 = 37 < 50 → PASS
Confidence: medium
折扣后得分: 1.0 × 0.85 = 0.85
```

**维度 5: 战略匹配 (20%)**
```
Score = 5/5 ≥ 3 → PASS
Confidence: high (完美匹配公司战略)
折扣后得分: 1.0 × 1.0 = 1.0
```

### 加权决策

| 维度 | PASS | 权重 | 置信度 | 加权得分 |
|------|------|------|--------|----------|
| 市场吸引力 | ✓ | 25% | medium (0.85) | 21.25% |
| 财务健康 | ✓ | 25% | medium (0.85) | 21.25% |
| 毛利率 | ✓ | 15% | medium (0.85) | 12.75% |
| 风险敞口 | ✓ | 15% | medium (0.85) | 12.75% |
| 战略匹配 | ✓ | 20% | high (1.0) | 20.00% |
| **合计** | **5/5** | **100%** | — | **88.00%** |

```
加权 PASS = 88% ≥ 60% → Go
决策置信度 = min(medium, medium, medium, medium, high) = medium
```

### 产出 `gate_1_review.md`

```markdown
# Gate 1 Review: L20

**Decision: Go** (Decision Confidence: medium)

## Market Attractiveness (TAM): PASS
TAM: $52.5B (threshold: $50M), confidence: medium
TAM far exceeds minimum threshold despite data source matching concerns.

## Financial Health (NPV/IRR): PASS
NPV: $3,890,218 (threshold: >$0), IRR: 196% (threshold: 12%), confidence: medium
Strong financial returns driven by high margin and moderate BOM.

## Gross Margin: PASS
Gross margin: 50.0% (threshold: 50.0%), confidence: medium
At threshold exactly — BOM increase of even $10 would trigger a FAIL.

## Risk Exposure: PASS
Risk score: 37 (threshold: 50), confidence: medium
Primary risks manageable; manufacturing precision is the key execution risk.

## Strategic Fit: PASS
Score: 5/5 (threshold: 3/5), confidence: high
Perfect strategic alignment with company's core mission.

## Summary
Gate 1 Review: Go (confidence: medium). 5/5 dimensions passed.

## Final Confirmation
Gate 1 recommends Go. Do you confirm?
```

### 用户确认

用户确认 **Go**，项目进入下一阶段。

---

## 第七步：最终审计（hw-pm-analyze，可选）

高价值项目（NPV > $1M）触发。加载 `hw-pm-analyze` 执行最终审计。

```
NPV = $3.89M > $1M → 触发审计
```

### 审计结果

```
交叉一致性:
[x] TAM 值在 competitive_analysis.json 与 discussion.md 一致
[x] ASP 在 business_case.md 与 competitive_analysis.md 的定价区段一致
[x] BOM 合计与分项之和一致
[x] NPV/IRR 在 JSON 与 MD 文件之间一致

来源可追溯性:
[!] TAM 来源 "Statista" — 缺少具体报告名称和发布日期
[!] BOM 成本为估算，无供应商报价支撑

格式完备性:
[x] 所有 confidence 值使用 high/medium/low
[x] 无 placeholder 文本
[x] JSON schema 合法

假设合理性:
[!] Year 1 销量 5,000 台对新品是否合理？— 需人工确认
[!] BOM $140 与工业级精度要求匹配？— 需工程团队验证
```

### 产出 `audit_report.md`

```markdown
# Audit Report: L20

**Gate Decision:** Go
**Audit Date:** 2026-06-07

## Summary
Issues Found — 2 warnings, 2 info

## Issues

🟡 W-01: TAM source incomplete
- File: competitive_analysis.json
- What: "Statista" without specific report name or date
- Recommendation: Add exact report title and publication date

🟡 W-02: BOM unvalidated
- File: business_case.md
- What: All BOM items marked as "estimate" with no supplier quotes
- Recommendation: Validate with 3+ supplier quotes before Phase 2

⚪ I-01: Year 1 volume assumption
- File: business_case.md
- What: 5,000 units assumed without channel capacity analysis
- Note: Acceptable for Phase 1, verify in Phase 2

⚪ I-02: BOM vs. precision requirements
- File: business_case.md
- What: $140 BOM may be low for industrial-grade precision components
- Note: Flag for engineering review in Phase 2
```

审计报告不修改原始产出物，标记问题后归档。

---

## 完整产出物清单

执行完整工作流后产生的所有文件：

```
projects/L20/
├── company.yaml
├── product_line.yaml
├── project.yaml
├── artifacts/
│   ├── phase_1_strategy/
│   │   ├── strategy_alignment.md
│   │   ├── competitive_analysis.md
│   │   ├── competitive_analysis.json
│   │   ├── user_research.md
│   │   ├── user_research.json
│   │   ├── business_case.md
│   │   ├── business_case.json
│   │   └── discussion.md
│   ├── gate_reviews/
│   │   └── gate_1_review.md
│   └── audit_report.md               # (可选)
└── changelog.yaml                     # 版本变更记录
```

---

## 常见问题

### 1. 多个子 agent 返回结果不一致怎么办？

skill 内置三层矛盾解决协议：
1. **数据驱动** — 比较双方数据的置信度和来源
2. **阈值判决** — 硬约束为仲裁标准
3. **呈报用户** — 归档双方论点，等待人工裁定

### 2. 某个子 agent 超时或失败怎么办？

每个 agent 是独立 Task，失败可单独重新派发该 agent：

```
Task("Market Analyst for L20")  # 仅重跑市场分析师
```

### 3. Review 返回 REJECT 怎么办？

Review 会列出具体不达标项。修复后重新提交 review：

```
退回原因: Layer 3 数据覆盖不达标 — TAM/SAM/SOM 缺失
修复: 重新派发市场分析师，确保 TAM/SAM/SOM 全部填写
然后: 重新加载 hw-pm-review
```

### 4. Gate Review 返回 No-Go 怎么办？

Gate Report 包含完整的 No-Go Detail 表格，列出每一项不达标的维度和数据差距。可直接用于向管理层汇报：

```markdown
| Dimension | Current State | Threshold | Gap | Source |
|-----------|--------------|-----------|-----|--------|
| TAM | $10M | $50M | -$40M | competitive_analysis.json |
```

### 5. 技能流程必须严格按顺序执行吗？

**依赖按 Phase 组织，cross-phase 技能可随时介入：**

```
Phase 1: hw-pm → (init?) → spec → (clarify?) → research → review → gate → (analyze?)
Phase 2: (Go) → prd
Phase 3: (prd complete) → design-review
Phase 4: (design freeze) → prototype (+ cert 并行)
Phase 5: (PVT pass) → npi → launch → EOL review
Cross: cost, triage (any phase)
```

任一前置技能未完成，后续技能无法启动。agent 加载技能时会自动检查前序产出物是否存在。

---

## Phase 2-5 概览

Phase 1 结果为 Go 后，进入产品开发阶段。技能按 Phase 顺序执行，部分技能可并行。

| Phase | 技能 | 职责 | 前序条件 |
|-------|------|------|----------|
| **2** | `hw-pm-prd` | PRD、技术规格、功能优先级矩阵 | Phase 1 Go |
| **3** | `hw-pm-design-review` | ID/MD/EE/FW 多轮设计评审与冻结 | PRD 完成 |
| **4** | `hw-pm-prototype` | EVT → DVT → PVT 验证管理 | 设计冻结 |
| **4** | `hw-pm-cert` | 认证矩阵、合规测试（与 prototype 并行） | 设计冻结 |
| **5** | `hw-pm-npi` | 供应商就绪、试产、爬坡计划 | PVT 通过 |
| **5** | `hw-pm-launch` | 定价、渠道、售后、上市后复盘 | NPI 完成 |
| **2-5** | `hw-pm-cost` | BOM 成本追踪、should-cost、cost-down（跨阶段） | 任意 |
| **2-5** | `hw-pm-triage` | 风险登记册、问题追踪、ECO 变更（跨阶段） | 任意 |

### Phase 2: PRD 定义

加载 `hw-pm-prd`，输入 Phase 1 产出物，输出：
- `artifacts/phase_2/prd.md` — 完整 PRD（背景、目标市场、用户故事、验收标准）
- `artifacts/phase_2/technical_spec.md` — 技术规格表（性能、接口、环境）
- `artifacts/phase_2/feature_matrix.md` — MoSCoW 优先级矩阵
- 更新 `project.yaml phase_status: design`

### Phase 3: 设计评审

加载 `hw-pm-design-review`，管理 3 轮评审（概念 → 详细 → 冻结前）：
- `artifacts/phase_3/design_review_log.md` — 会议纪要
- `artifacts/phase_3/design_issues.md` — 问题追踪
- `artifacts/phase_3/design_freeze.md` — 冻结签核
- 更新 `project.yaml phase_status: validate`

### Phase 4: 验证与认证

加载 `hw-pm-prototype`（必需）和 `hw-pm-cert`（可选，可并行）：
- EVT → DVT → PVT 三阶段，每阶段有 exit criteria
- 认证矩阵覆盖所有目标市场
- 更新 `project.yaml phase_status: manufacturing`

### Phase 5: NPI 与上市

加载 `hw-pm-npi` → 完成后加载 `hw-pm-launch`：
- 供应商评估、试产、爬坡计划、定价、渠道、售后
- 上市后 30/60/90 天复盘，6/12 个月回顾
- 更新 `project.yaml phase_status: eol`

### Cross-Phase 技能

`hw-pm-cost` 和 `hw-pm-triage` 可随时加载，不依赖 phase_status：
- 成本管理：设计阶段设定目标 BOM，原型阶段验证，量产后 cost-down
- 风险追踪：三防机制 — 预防（风险登记册）、检测（问题追踪）、纠正（ECO）

---

## 参考

- [技能总览 (hw-pm)](../skills/hw-pm/SKILL.md)
- [快速初始化 (hw-pm-init)](../skills/hw-pm-init/SKILL.md)
- [需求定义 (hw-pm-spec)](../skills/hw-pm-spec/SKILL.md)
- [用户澄清 (hw-pm-clarify)](../skills/hw-pm-clarify/SKILL.md)
- [并行调研 (hw-pm-research)](../skills/hw-pm-research/SKILL.md)
- [完备性审查 (hw-pm-review)](../skills/hw-pm-review/SKILL.md)
- [投资决策 (hw-pm-gate)](../skills/hw-pm-gate/SKILL.md)
- [最终审计 (hw-pm-analyze)](../skills/hw-pm-analyze/SKILL.md)
- [PRD 定义 (hw-pm-prd)](../skills/hw-pm-prd/SKILL.md)
- [设计评审 (hw-pm-design-review)](../skills/hw-pm-design-review/SKILL.md)
- [原型验证 (hw-pm-prototype)](../skills/hw-pm-prototype/SKILL.md)
- [认证合规 (hw-pm-cert)](../skills/hw-pm-cert/SKILL.md)
- [NPI 导入 (hw-pm-npi)](../skills/hw-pm-npi/SKILL.md)
- [上市管理 (hw-pm-launch)](../skills/hw-pm-launch/SKILL.md)
- [成本控制 (hw-pm-cost)](../skills/hw-pm-cost/SKILL.md)
- [风险追踪 (hw-pm-triage)](../skills/hw-pm-triage/SKILL.md)
