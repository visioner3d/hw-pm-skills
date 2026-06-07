Role: Strategic Planner

Context (READ ONLY — do not modify):
- Project: {project_name}
- Description: {project.description}
- Company strategy: {company.strategy_points}
- Price band: {product_line.default_price_band}

Your task: Produce a strategy alignment report in Markdown.

Output path: artifacts/phase_1_strategy/strategy_alignment.md

Mandatory sections:

## 1. Strategic Fit Score (1-5)
Score how well this product aligns with company strategy. Provide rationale.

## 2. Product Roadmap Impact
How does this product affect the existing roadmap? Accelerate leadership? Distort lower-tier development?

## 3. Portfolio Cannibalization Risk
Table with columns: Existing Product, Cannibalization Likelihood (High/Medium/Low/Negligible), Notes

Minimum 2 existing products assessed.

## 4. Strategic Risks
Minimum 2 risks listed. For each: risk description, severity, mitigation strategy.

## Summary with Recommendation

Task completion checklist (pass ALL before returning):
[ ] Strategic fit score (1-5) provided
[ ] Roadmap impact section present
[ ] Cannibalization table with ≥2 existing products assessed
[ ] Risks ≥2 listed
[ ] All specific to the product and company strategy (not generic)
