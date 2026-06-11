# User Research Report — L3D-10

> **Project:** L3D-10 — 高精度线激光 3D 相机 (High-precision line laser 3D camera)
> **Industry:** 3C (Computer, Communication, Consumer Electronics)
> **Price Band:** $200 – $300 USD
> **Date:** 2026-06-08
> **Confidence Note:** Based on industry knowledge of 3C inspection workflows. Specific user interview data was not available; assumptions are marked with confidence levels.

---

## 1. User Personas

### Persona 1: Precision-Focused Quality Engineer

| Attribute | Detail |
|-----------|--------|
| **Name** | "Precision-Focused Quality Engineer" |
| **Demographics** | Age 28–45; holds a degree in mechanical/electrical/optical engineering; works at a 3C contract manufacturer (EMS) or OEM in Shenzhen/Kunshan/Suzhou; annual income $25K–$55K USD |
| **Goals** | Detect micro-defects (scratches, solder joint voids, tilts) on high-speed production lines (60+ units/min); reduce false rejection rate below 1%; integrate inspection data into MES for traceability |
| **Frustrations** | Existing sensors at $500+ are cost-prohibitive for multi-station deployment; cheaper alternatives ($150–$200) lack enough resolution (Z-axis noise >10 µm), causing escapes; calibration drifts after 4–6 hours in factory-floor temperature swings (25°C–45°C) |
| **Usage Context** | Mounts camera on gantry or robot arm above a conveyor belt in a factory with ambient dust/vibration. Inspects 5–20 different SKUs per shift. Typically uses a PC with GigE/U2P interface and runs proprietary vision software. **Confidence: Medium** |

### Persona 2: Cost-Conscious Production Line Manager

| Attribute | Detail |
|-----------|--------|
| **Name** | "Cost-Conscious Production Line Manager" |
| **Demographics** | Age 32–50; role: production supervisor / process engineering manager at mid-sized 3C assembly plant (200–1,000 employees); annual income $20K–$40K USD; responsible for line OEE and capex budget |
| **Goals** | Minimize per-station inspection cost to enable 100% inline QA across 10+ stations; reduce training time for new inspectors; justify ROI to plant director within one quarter |
| **Frustrations** | Budget constraints force spot-check (sampling 1–5%) rather than 100% inspection; replacing existing 2D camera systems requires retraining operators; downtime per sensor swap eats into OEE targets |
| **Usage Context** | Evaluates sensors alongside integrators during line retrofits. Needs plug-and-play compatibility with existing industrial PCs and PLCs (EtherCAT/Modbus). Relies on vendor application notes to compare specs. **Confidence: Medium** |

### Persona 3: Vision System Integrator

| Attribute | Detail |
|-----------|--------|
| **Name** | "System Integrator Architect" |
| **Demographics** | Age 30–55; technical lead at a machine vision integration firm; serves 3C, automotive, and semiconductor clients; annual income $40K–$80K USD |
| **Goals** | Recommend one sensor platform that covers 80% of client inspection needs to reduce qualification effort; minimize SKU count in BOM to simplify supply chain; deliver turnkey solutions with ≤2 weeks of integration |
| **Frustrations** | Each client requires a different sensor brand, complicating spares inventory; inconsistent SDK quality across vendors causes integration delays; lack of standardized 3D calibration targets makes field recalibration unreliable |
| **Usage Context** | Deploys at greenfield and brownfield lines. Writes C++/C# wrappers around vendor SDKs. Needs a sensor with open APIs, good documentation, and sample code. Values pre-calibrated factory units that ship with a NIST-traceable certificate. **Confidence: Low** |

---

## 2. Pain Points

| Rank | Pain Point | Severity | Frequency | Description | Confidence |
|------|-----------|----------|-----------|-------------|------------|
| 1 | Cost barrier to 100% inline inspection | High | High | Current high-precision line laser 3D cameras cost $500–$1,200, making multi-station deployment (10+ lines) prohibitive for mid-tier 3C plants. Forces reliance on sampling inspection, missing 2–5% of defects. | High |
| 2 | Insufficient Z-axis accuracy in budget sensors | High | High | Sub-$200 sensors exhibit Z-axis noise >10–15 µm, insufficient for PCB solder joint inspection (requires ≤5 µm) and connector pin coplanarity (requires ≤3 µm). Leads to false passes on critical defects. | Medium |
| 3 | Calibration drift under thermal variation | High | Medium | Factory-floor temperature swings (25°C–45°C) cause laser line shift and sensor drift within 4–6 hours. Requires frequent recalibration halts, reducing effective line uptime by 3–5%. | Medium |
| 4 | SDK fragmentation and poor documentation | Medium | High | Each vendor ships a proprietary SDK with inconsistent API design. Integrators spend 2–4 weeks per sensor brand just on driver integration. Poor documentation and scarce sample code compound the problem. | Medium |
| 5 | Difficult field recalibration without metrology expertise | Medium | Medium | After sensor replacement, field recalibration requires specialized gauge blocks and trained technicians. Smaller factories lack in-house metrology staff, leading to extended downtime (1–2 days) per swap. | Low |

---

## 3. Jobs-to-be-Done

1. "Help me **inspect 100% of PCBs inline at full line speed** so that **I eliminate escape defects and avoid customer returns**."
2. "Help me **deploy the same sensor across 10+ inspection stations under budget** so that **I can standardize spares and reduce integration cost per line**."
3. "Help me **quickly recalibrate the camera after field replacement** so that **untrained line operators can restore service within 30 minutes instead of 2 days**."
4. "Help me **detect sub-5 µm Z-axis defects on shiny/mirror surfaces** so that **I catch open-solder and tilted-component failures that 2D cameras miss**."
5. "Help me **integrate a 3D sensor with my existing vision software stack** so that **I do not need to rewrite my inspection pipeline for each supplier**."
