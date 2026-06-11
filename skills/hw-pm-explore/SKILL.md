---
name: hw-pm-explore
description: "Use after spec is complete but BEFORE research dispatch — explores adjacent possibilities, cross-industry analogies, and technology trends to ensure the product direction hasn't prematurely converged on a suboptimal framing. Optional. Lightweight. Anti-template."
---

# Product Exploration & Divergent Thinking (hw-pm-explore)

## Overview

Every other skill in hw-pm is **convergent** — it takes ambiguity and narrows it toward a decision. `hw-pm-explore` is the only **divergent** skill. Its sole purpose is to widen the possibility space before committing to a specific product direction.

This skill does not produce a decision, a grade, or a checklist. It produces a **possibility landscape** — a structured set of "what if" framings that the PM can consider, borrow from, or reject before entering research.

**Core belief:** The first idea is rarely the best idea. A 15-minute exploration before a multi-week research pipeline is the highest-leverage investment in the entire product lifecycle.

## When to Use

- Spec is complete (`hw-pm-spec` hard gate passed), but research has NOT yet started
- The PM wants confidence that they are researching the right thing, not just the first thing
- The product category is unfamiliar to the PM (see `pm_background` in project.yaml)
- The product involves new technology or an emerging market

**Don't use when:**
- The PM has deep domain expertise and a clear, validated conviction about the direction
- Time is critically constrained (skip to research immediately)
- The product is a minor iteration on an existing successful product
- Research output files already exist (you've already committed to a direction)

## Design Constraints

This skill is intentionally **anti-template**. It must NOT produce a checklist, a score, a gate, or a filled form. If the output feels like a template, the skill has failed its purpose.

| Principle | Implementation |
|-----------|---------------|
| **Optional** | PM decides whether to invoke it. The routing system offers it, never requires it. |
| **Anti-template** | No standard output format. The output is a narrative map, not a filled cell. |
| **Lightweight** | ≤ 1 conversation turn. No subagents dispatched. A single web search for cross-industry inspiration. |

## Process

### Step 1: Read the Spec

Load `project.yaml` and understand the current product description, target price band, and competitive context. This is the "baseline framing" — the idea as it currently exists.

### Step 2: Ask the Generative Question

Present exactly ONE question to the PM:

```
当前产品的核心假设是：这个产品是一个 {核心功能描述}。

在我们进入详细调研之前，我想问一个开放式问题：

"如果这个产品的目标用户已经完美满足了他们的需求，
但完全不是通过我们正在构思的这种方式——他们可能在使用什么替代方案？
或者换个角度：这个产品有没有可能本质上不是我们描述的东西，
而是一个更根本的别的什么？"

请自由回答，不用考虑可行性。2-3句话即可。
也可以直接说"不需要探索，我对当前方向很确定"来跳过整个explore阶段。
```

This is the only mandatory interaction. If the PM says "skip" or "确定", exit immediately and route to research. Respect the PM's conviction.

If the PM engages, continue.

### Step 3: Three Lenses of Exploration

Use these three lenses to broaden the PM's thinking. Do not ask the PM to fill forms — instead, think out loud with them.

#### Lens 1: Adjacent Possible (相邻可能)

"What is one small change to this product that would make it address a completely different market?"

Example: A 3D laser scanner for industrial inspection → what if it were repurposed for surgical navigation? Same core technology, entirely different market, different price tolerance, different certification path.

**Action:** Brainstorm 2-3 adjacent markets. Quick web search if helpful: `"{core technology}" applications beyond {current industry}`.

#### Lens 2: Cross-Industry Analogy (跨行业类比)

"Who has solved a structurally similar problem in a completely different industry, and what can we learn?"

Example: The problem of "real-time quality inspection of moving objects" has been solved in:
- Food processing (optical sorting of grains at 10m/s)
- Semiconductor (wafer inspection at nanometer precision)
- Automotive (ADAS object detection in real-time)

Each industry made different trade-offs (speed vs precision, cost vs reliability). Which trade-off pattern fits our market?

**Action:** Do ONE web search: `"cross-industry innovation" {product domain}` or `"technology transfer" {core technology}`. Summarize 2-3 interesting analogies in 1-2 sentences each.

#### Lens 3: Technology Trend Vector (技术趋势矢量)

"If we project current technology trends forward 3-5 years, does our product concept become more or less relevant?"

Consider:
- Sensors: are they getting cheaper/faster/smaller along a curve that intersects our product?
- Compute: is edge AI making centralized processing obsolete for our use case?
- Connectivity: does ubiquitous 5G/WiFi change how our product delivers value?
- Manufacturing: are new processes (3D printing, advanced molding) enabling form factors we haven't considered?

**Action:** Quick web search: `"{core technology}" trend 2026 2027` or `"future of {product category}"`. Identify 1-2 trend vectors that could either supercharge or obsolete the current concept.

### Step 4: Synthesize — The Possibility Landscape

After exploring the three lenses, produce a brief synthesis. This is NOT a recommendation — it is a map with annotated territory.

```markdown
## Possibility Landscape: {project_name}

**Baseline framing:** {one-line description from spec}

### Alternative framings surfaced during exploration:

1. **{Framing A}** — {one sentence}. Different market: {which}. New risk: {what}. New opportunity: {what}.
2. **{Framing B}** — {one sentence}. Different market: {which}. New risk: {what}. New opportunity: {what}.

### Cross-industry lessons:
- From {industry}: {one insight we could borrow}
- From {industry}: {one insight we could borrow}

### Technology trend check:
- {Trend} — {implication for our concept: threat / tailwind / neutral}
- {Trend} — {implication for our concept: threat / tailwind / neutral}

### What we are NOT changing:
- The core problem we're solving is still valid: {restate JTBD essence}
- The investment thesis structure (Phase 1 gate criteria) remains applicable

### PM's call:
The current spec direction is {still the best framing / enriched but unchanged / worth re-examining aspects X and Y}.

Whether any of these alternatives warrant deeper investigation is the PM's judgment.
If yes → update spec or run hw-pm-clarify before research.
If no → proceed to hw-pm-research with the baseline framing, now held with more awareness of what it is NOT.
```

### Step 5: Route Forward

Two possible outcomes:

**Outcome A — PM stays with baseline:**
```
No changes to spec. Proceed to hw-pm-research.
The exploration has increased conviction — you now know not just what
you're building, but what you're choosing NOT to build.
```

**Outcome B — PM wants to adjust:**
```
The spec needs refinement based on exploration insights.
Route to hw-pm-clarify to update the spec, then proceed to research.
```

## What This Skill Does NOT Do

- **Convince the PM to change direction.** Its job is to surface alternatives, not advocate for them.
- **Produce a scored comparison.** There is no "Option A scores higher than Option B." PM judgment is the only scoring mechanism.
- **Run competitive analysis.** That's hw-pm-research's Market Analyst.
- **Validate feasibility.** That's the entire pipeline downstream.
- **Add a gate or checklist.** If this skill adds process friction, it has failed.

## Relationship to Other Skills

```
hw-pm-spec → [hw-pm-clarify] → [hw-pm-explore] → hw-pm-research
                (optional)       (optional)
```

- **hw-pm-clarify**: Resolves ambiguity in an existing spec. Convergent.
- **hw-pm-explore**: Surfaces new possibilities beyond the spec. Divergent.
- Run clarify FIRST if the spec has known ambiguities. Run explore AFTER the spec is clear, to test whether a clear spec is the right spec.

## Common Mistakes

- **Running explore as a mandatory gate.** "Phase 1 checklist says explore must run." → This is an optional pre-research reflection, not a requirement. Skip if the PM has conviction.
- **Treating explore output as a decision.** "The landscape says Framing A is better, let's change." → The PM decides. The landscape only informs.
- **Over-exploring.** "Let me research all 5 alternative framings in depth." → No. This is a 15-minute surface scan. If an alternative genuinely warrants depth, go back to hw-pm-spec with the new framing and run the full pipeline.
- **Confusing explore with research.** Explore is divergent (what else could this be?). Research is convergent (is this specific thing viable?). Never dispatch subagents from explore.
- **Allowing explore to delay conviction.** "Let me explore a bit more..." → Once. One pass. Then decide.

## Why This Skill Exists

Because the biggest risk in hardware product management is not that a product fails validation — it's that you spend 18 months and millions validating the wrong product. A 15-minute pre-research exploration is the cheapest insurance against that risk in the entire pipeline.
