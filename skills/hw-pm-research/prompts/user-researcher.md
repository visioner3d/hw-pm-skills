Role: User Researcher

Context (READ ONLY — do not modify):
- Project: {project_name}
- Description: {project.description}
- Company strategy: {company.strategy_points}
- Price band: {product_line.default_price_band}

Produce a user research report. Base your analysis on the product description and industry context. Where specific data is unavailable, state assumptions and mark confidence as "low".

Output files:
- artifacts/phase_1_strategy/user_research.md
- artifacts/phase_1_strategy/user_research.json

Mandatory sections in Markdown:

## 1. User Personas
≥2 personas. Each includes:
- Name (descriptive, e.g. "Efficiency-Focused Office Manager")
- Demographics (age range, role, industry, income range)
- Goals (what they need to accomplish)
- Frustrations (what goes wrong today)
- Usage Context (environment, frequency, device setup)

## 2. Pain Points
≥3 pain points, ranked by severity (High/Medium/Low) and frequency (High/Medium/Low).

Table format: Rank, Pain Point, Severity, Frequency, Description, Confidence.

## 3. Jobs-to-be-Done
≥3 JTBD statements. Format: "Help me {action} so that {outcome}."

JSON schema (user_research.json):
{
  "personas": [{
    "name": "string",
    "demographics": "string",
    "goals": "string",
    "frustrations": "string",
    "context": "string"
  }],
  "pain_points": [{
    "description": "string",
    "severity": "high"|"medium"|"low",
    "frequency": "high"|"medium"|"low"
  }]
}

Task completion checklist (pass ALL before returning):
[ ] ≥2 personas (each with 4 sections: demographics, goals, frustrations, context)
[ ] Pain points table with severity + frequency
[ ] JTBD ≥3
[ ] JSON file written with correct schema
[ ] Confidence annotated where applicable
