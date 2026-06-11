# Gate 1 Review: L3D-10

**Decision: Go** (Decision Confidence: Medium)
**Date:** 2026-06-08

---

## Market Attractiveness (TAM): PASS

| Metric | Value | Threshold | Confidence | Weight |
|--------|-------|-----------|------------|--------|
| TAM | $450,000,000 | $50,000,000 | Medium | 25% |
| Raw Score | 1.00 (capped) | ≥ 1.00 | — | — |
| Effective Contribution | **21.25%** | — | — | 25% × 0.85 |

The global 3D line laser camera market is estimated at $450M (Data Insights Market, 2024, 10% CAGR). Broader adjacent markets (Line Laser Scanning 3D Camera) are $2.5B. The narrow figure is used conservatively. 3C electronics manufacturing represents ~25% of the market, yielding SAM of $112.5M. The L3D-10 targets a new "ultra-budget" sub-$300 segment where no direct competitor currently exists — creating greenfield opportunity but also category-creation risk.

**Rationale:** TAM exceeds threshold by 9×. Even at 0.85× confidence discount, the margin is substantial.

---

## Financial Health (NPV/IRR): PASS

| Metric | Value | Threshold | Confidence | Weight |
|--------|-------|-----------|------------|--------|
| NPV (at 12% WACC) | $63,487 | > $0 | Medium | 25% |
| IRR | 27.2% | > 10% (risk-free 5% + premium 5%) | Medium | — |
| Effective Contribution | **21.25%** | — | — | 25% × 0.85 |

**Assumptions:** ASP $250, Year 1/2/3 units: 500/1,500/3,000, Fixed costs $80K/yr, Dev investment $150K, Tax 25%.

**Sensitivity:** Base case NPV is robust. However, combined worst-case scenario (−10% ASP, +15% BOM, −20% volume) yields negative NPV of −$15,438. This is the primary risk to financial health.

**Rationale:** Both NPV and IRR thresholds are met with comfortable margins. NPV exceeds threshold 6.3×. IRR is 2.7× the hurdle rate.

---

## Gross Margin: PASS

| Metric | Value | Threshold | Confidence | Weight |
|--------|-------|-----------|------------|--------|
| Gross Margin | 51.0% | 50.0% | Medium | 15% |
| Effective Contribution | **12.75%** | — | — | 15% × 0.85 |

**BOM:** $85 (9 line items). Key components: FPGA ($18), CMOS sensor ($15), optics ($12), laser diode ($10), housing ($9).

**Risk:** At only 1% above threshold, margin is vulnerable. A $10 increase in BOM (to $95) drops GM to 47%. The unvalidated assumption that $85 BOM delivers <10µm precision is the key risk.

**Rationale:** Meets threshold but with minimal buffer. Confidence discount reflects unvalidated BOM.

---

## Risk Exposure: PASS

| Risk | Severity (1-5) | Likelihood (1-5) | Score |
|------|----------------|------------------|-------|
| Price-band margin compression | 4 | 3 | 12 |
| Domestic competitor price war | 4 | 3 | 12 |
| Precision vs BOM (unvalidated) | 3 | 3 | 9 |
| Combined worst-case (negative NPV) | 4 | 2 | 8 |
| **Total Risk Score** | | | **41** |

| Metric | Value | Threshold | Confidence | Weight |
|--------|-------|-----------|------------|--------|
| Risk Score | 41 | < 50 | Medium | 15% |
| Effective Contribution | **12.75%** | — | — | 15% × 0.85 |

**Rationale:** Risk score of 41 is below the 50 threshold. Margin compression and price competition are the most significant risks. The combined worst-case NPV sensitivity is the financial tipping point — managed through minimum volume commitments.

---

## Strategic Fit: PASS

| Metric | Value | Threshold | Confidence | Weight |
|--------|-------|-----------|------------|--------|
| Fit Score | 4/5 | ≥ 3/5 | High | 20% |
| Effective Contribution | **20.00%** | — | — | 20% × 1.00 |

**Alignment:** L3D-10 directly supports v3d's strategy as a "trusted 3D sensor supplier" by democratizing 3D inspection. Fills a critical mid-range gap in the portfolio between 2D systems ($150–250) and high-end profilers ($600+). Cannibalization of LL-500 (Medium) and VS-200 (Medium) is partly strategic — planned displacement of 2D systems.

**Deduction (−1):** The low price band partially dilutes "premium supplier" positioning if not paired with a clear reliability narrative.

**Rationale:** Strong fit with no strategic conflicts.

---

## Summary

| Dimension | Status | Raw Weight | Confidence | Effective Weight |
|-----------|--------|-----------|------------|------------------|
| Market Attractiveness | PASS | 25% | Medium (0.85×) | 21.25% |
| Financial Health | PASS | 25% | Medium (0.85×) | 21.25% |
| Gross Margin | PASS | 15% | Medium (0.85×) | 12.75% |
| Risk Exposure | PASS | 15% | Medium (0.85×) | 12.75% |
| Strategic Fit | PASS | 20% | High (1.00×) | 20.00% |
| **Weighted Total** | | **100%** | | **88.00%** |

**Decision Threshold:** ≥ 60% for Go
**Weighted PASS:** 88.00% ✅

**Result: GO** — All 5 dimensions pass. Weighted score significantly exceeds the 60% threshold.

### Conditions from Review (carried forward)

1. **Volume floor commitment:** Combined worst-case (ASP -10%, BOM +15%, volume -20%) yields negative NPV. Secure minimum volume commitments from 3+ pilot customers before full production go-ahead.
2. **BOM validation:** Confirm $85 BOM delivers <10µm Z-repeatability through preliminary supplier quotes before PRD freeze.
3. **Category creation risk:** Zero direct competitors at $200–$300 means demand is unvalidated. Document explicit acceptance of category-creation risk at Phase 2 kickoff.

---

## Final Confirmation

**Gate 1 recommends: GO** — proceed to Phase 2 (PRD).

Do you confirm this decision?
