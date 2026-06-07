# PRD: 硬件产品经理多智能体工作流系统 (hw-pm)

## Problem Statement

硬件产品经理的核心工作是从投资决策角度判断一个产品是否值得投入资源。然而现有决策流程依赖分散的邮件、表格和会议，决策依据缺乏统一的数据溯源和量化标准。产品经理需要同时处理市场调研、竞品分析、成本估算、财务建模等多维度信息，工作量大且容易遗漏关键因素。

需要一套以"产品即投资品"为核心理念的多智能体系统，自动执行硬件产品开发流程，产出结构化的决策依据。

## Solution

构建一个基于 Python + AutoGen 多智能体框架的 CLI 工具 `hw-pm`，将硬件产品开发流程拆为五个阶段（Squad 模式），每个阶段由多个领域智能体协作完成任务，最终产出量化的投资决策报告。

首期 MVP 覆盖 Phase 1（战略与调研）和 Gate 1（投资决策评审），验证"产品即投资品"这一核心命题。

## User Stories

1. 作为产品经理，我想用一条命令初始化产品项目并继承公司的战略和投资阈值配置，以便快速启动分析而不必每次都重新输入基础信息
2. 作为产品经理，我只输入产品的一句话描述，系统就能并行调度市场分析师、用户研究代理和商业分析师同步开展调研工作，以便节省串行调研的时间
3. 作为产品经理，我期望市场分析师自动搜索竞品信息并生成结构化的竞品分析报告，以便我快速了解竞争格局
4. 作为产品经理，我期望商业分析师根据产品规格自动估算单台 BOM 成本和建立单位经济模型，以便我在概念阶段就能判断财务可行性
5. 作为产品经理，我期望产出物中每个数据点都标注置信度（high/medium/low）和来源，以便我知道哪些结论是可靠的、哪些需要人工进一步核实
6. 作为产品经理，我期望系统在 Phase 1 完成后自动执行 Gate Review，按量化维度（市场规模、NPV、毛利、风险敞口、战略匹配）输出 Go/No-Go 判决，以便我做出有数据支撑的投资决策
7. 作为产品经理，我可以在每个关键决策点（如 Gate Review、高成本影响决策）暂停并确认，以便我在重要节点保留最终决策权
8. 作为产品经理，我可以配置公司级战略信息（如公司战略要点、毛利率底线、投资阈值），这些信息自动被所有项目继承，以便保证战略一致性
9. 作为产品经理，我可以按产品线定义默认配置（品牌定位、价格带、关键竞品），新项目只需选择产品线即可继承这些设定
10. 作为产品经理，我可以通过 `hw-pm status` 查看当前项目的阶段进度、产出物列表和 Gate 评审结果，以便掌握项目整体状态
11. 作为产品经理，我期望所有 agent 的完整对话日志被持久化保存，以便事后审计决策过程和追溯每个结论的来源
12. 作为产品经理，当市场分析师和商业分析师给出相反建议时，产品总监能根据数据置信度或配置中的硬约束做出仲裁，以便及时推进决策而非陷入僵局
13. 作为产品经理，我期望产出物有明确的版本管理（文件快照 + changelog），后续阶段发现问题可以回溯发起修改请求，以便支持产品开发的迭代特性
14. 作为产品经理，我可以自定义产出物模板（竞品分析、PRD、Gate Report 等）来适配组织内部的标准格式，系统内置默认模板作为开箱即用的起点
15. 作为产品经理，我可以选择不同 LLM 模型分配给不同 agent（如关键决策用 Claude Opus，批量调研用 Claude Haiku），以便在成本和产出质量之间取得平衡
16. 作为产品经理，Phase 1 完成后如果投资决策为 No-Go，系统能清晰列出每一项不达标的维度和具体数据依据，以便我向管理层汇报终止决策的理由

## Implementation Decisions

### 核心架构

- 采用 Python + AutoGen 多智能体框架，所有 agent 类型均为 AutoGen 的 AssistantAgent
- 五阶段 Squad 模型：每阶段由 Squad Lead 作为 GroupChatManager 协调
- 混合执行模式：独立调研任务并行派发（Task Dispatch），需要讨论和决策的任务通过 GroupChat 串行进行
- 阶段间通过结构化文档（Markdown + JSON）传递信息，下游只读上游产出
- 产品总监作为全局协调者，负责阶段调度和 Gate Review
- 项目管理者作为跨阶段角色，维护时间线和风险登记册

### MVP 范围

- 仅实现 Phase 1（战略与调研）+ Gate 1（投资决策评审）
- Phase 1 包含 4 个 agent：战略规划师、市场分析师、用户研究代理、商业分析师
- 产品总监同时充当 Squad Lead 和 Gate Reviewer

### 配置体系

- 三级配置继承：公司级 (company.yaml) → 产品线级 (product_line.yaml) → 项目级 (project.yaml)
- 公司级配置包含：公司战略要点、默认投资阈值、行业领域、合规要求
- 产品线级配置包含：品牌定位、目标毛利率、默认价格带、关键竞品
- 项目级仅填写差异化信息：项目名、产品一句话描述
- 配置以本地 YAML 文件存储，不使用数据库
- LLM 模型配置由用户在配置文件中自行指定，系统不做硬编码绑定

### Gate Review 机制

- 产品总监读取 Phase 1 的 4 份 agent 产出后，按投资决策维度模板打分
- 评分维度：市场吸引力（TAM 阈值）、财务健康（NPV>0 且 IRR>无风险利率+风险溢价）、毛利率（≥配置阈值）、风险敞口（<公司可承受上限）、战略匹配（主观评分）
- 每个维度结合数据置信度综合评定
- 输出：Go/No-Go 判决 + 决策置信度 + 逐维度评语
- 低置信度数据不触发额外的 agent 补充询问，直接降低该决策维度的置信度
- Gate Report 呈现给用户做最终确认

### Agent 工具权限

- 工具按需显式授权，默认无工具
- 市场分析师：web_search（Firecrawl）、read_file
- 商业分析师：financial_calc（NPV/IRR/TAM 计算）、read_file
- 战略规划师、用户研究代理：read_file（只读公司/产品线配置和已有产出物）
- 产品总监：read_file、write_file、gate_review
- Agent 不能直接写文件，产出通过产品总监写入

### 数据可信度

- 每个 JSON 数据点标注 `confidence`（high|medium|low）和 `source`
- 低置信度数据在 Gate Review 中降低该维度的决策置信度

### 产出物格式

- 调研/分析类 agent（市场分析师、用户研究代理、商业分析师）：Markdown + JSON 双输出
- 决策类 agent（战略规划师、产品总监）：Markdown 输出
- Stakeholder 报告：纯 Markdown
- 默认模板内置，用户可自定义覆盖

### 版本管理

- 文件快照方式（如 prd_v1.md, prd_v2.md）配合 changelog.yaml 记录变更原因
- 不使用 Git

### 冲突解决

- 数据驱动：双方各自给出数据依据，产品总监比较置信度
- 阈值判决：以配置中硬约束为仲裁标准（如毛利率底线）
- 呈报用户：数据不足或战略级分歧，归档双方论点，暂停等待人工裁定

### 任务完成判定

- 每个 task 预设结构化要求（章节、最小字数、必须表格、必须引用来源等）
- Agent 完成任务后自检 checklist，全部满足则标记完成
- 超最大轮次未完成则 Squad Lead 接管

### CLI 接口

命令：`hw-pm setup`, `hw-pm product-line create`, `hw-pm init`, `hw-pm run <phase>`, `hw-pm status`, `hw-pm review`

### 模块划分

- `cli/commands.py`：CLI 入口
- `core/config/manager.py`：三级配置加载与合并
- `core/agents/factory.py`：Agent 工厂
- `core/agents/roles/`：各角色 system prompt + 工具声明
- `core/agents/tools/`：工具函数（web_search, financial_calc, file_ops）
- `core/squads/`：Squad 定义（GroupChat + Task Dispatch）
- `core/orchestration/orchestrator.py`：阶段调度
- `core/orchestration/gate_review.py`：Gate 模板评分
- `core/orchestration/project_manager.py`：时间线 + 风险
- `core/artifacts/storage.py`：文件读写 + 版本快照
- `core/artifacts/changelog.py`：变更记录
- `templates/`：内置模板（Markdown）

## Testing Decisions

- 测试外部行为而非实现细节
- Unit 测试：ConfigManager 合并逻辑、FinancialCalc 计算正确性、TemplateEngine 渲染、FileStorage 读写与快照机制、JSON 置信度格式校验
- Integration 测试：Phase 1 Squad 对话测试（使用 AutoGen 的 Recorded Dialog 回放功能，Mock LLM 验证产出结构完整性）、Gate Review 评分逻辑（给定固定产出物输入，验证评分和 Go/No-Go 输出）
- E2E 测试：完整 MVP 流程（从 init 到 run 到 gate review），使用预设的智能音箱产品场景（fixture）
- 测试框架：pytest，fixture 目录存放预设产品场景数据

## Out of Scope

- Web 界面
- Phase 2-5（产品定义、设计评审、原型与测试、量产与运营）
- 项目管理者 Dashboard 和自动风险预警
- 与外部企业系统（ERP/PLM）的对接
- 多项目并发执行
- 联网实时查询元器件价格
- 多语言支持

## Further Notes

- 系统设计预留了 Web 层扩展接口：`core/` 模块与 UI 完全解耦，未来可直接挂接 FastAPI + WebSocket 前端
- 除 Phase 1 外的角色定义（定价策略师、工业设计代理、供应链协调员等）设计已完成，MVP 后按优先级排期实现
- 推荐使用 Anthropic Claude 系列模型（关键决策用 Opus，批量任务用 Haiku），但模型选择由用户在配置文件中自由指定
