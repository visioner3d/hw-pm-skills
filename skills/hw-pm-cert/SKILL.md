---
name: hw-pm-cert
description: "Use during DVT phase to manage regulatory certifications (FCC/CE/UL/CCC etc.), track testing schedules, and log compliance deviations."
---

# Certification & Compliance Management (hw-pm-cert)

## Overview
Manages product certifications across target markets. Can run in parallel with prototype validation. Tracks certification matrix, test schedules, and compliance deviations.

## When to Use
- Design freeze complete, entering validation phase
- Need to track certification status across multiple target markets

## Process
1. Identify target markets and their certification requirements
2. Build cert_matrix.md (market × standard × status × lead time × cost)
3. Schedule certification testing with labs
4. Track test results, log deviations and waivers
5. Update compliance status as results come in
6. Output cert_compliance.md with final compliance declaration

## Output Format
Write to artifacts/phase_4/:
- cert_matrix.md — full certification matrix
- cert_schedule.md — test schedule with lab bookings
- cert_compliance.md — compliance declaration with deviations

## Hard Gate Checklist
```
[ ] Certification matrix covers all target markets
[ ] All required certifications planned or completed before production
[ ] Deviations documented with risk assessment
[ ] User confirmed cert status
```

## Common Mistakes
- Missing a target market's certification requirement
- Not accounting for certification lead times (can be 4-12 weeks)
- Assuming certification carries over from previous product without review
