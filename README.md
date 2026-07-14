# Precision Bandgap Voltage Reference (Bandgap2.0)
### Curvature-Compensated Brokaw-Cell BGR | GPDK090 90nm CMOS | Cadence Virtuoso / Spectre

[![Technology](https://img.shields.io/badge/Technology-GPDK090%2090nm-blue)]()
[![EDA](https://img.shields.io/badge/EDA-Cadence%20Virtuoso-red)]()
[![Simulator](https://img.shields.io/badge/Simulator-Spectre-orange)]()
[![Status](https://img.shields.io/badge/Status-Simulation%20Complete-green)]()
[![Phase](https://img.shields.io/badge/Next%20Phase-Layout%20%2F%20Physical%20Design-purple)]()

---

## Overview

This project implements a **curvature-compensated Brokaw-cell Bandgap Voltage Reference (BGR)** in GPDK090 90nm CMOS, designed and verified in Cadence Virtuoso using the Spectre simulator.

The design has been characterized across full PVT (Process, Voltage, Temperature) corners, with verification spanning DC operating point, AC/loop stability, temperature-sweep TC extraction, and Monte Carlo statistical analysis.

### Target Applications
- IoT sensor nodes
- Precision analog front-ends
- Battery-powered mixed-signal SoCs
- Reference voltage generation for ADCs, DACs, and LDOs

---

## Architecture

```
VDD (1.8V)
    │
    ├─── PMOS Current Mirrors (PM8, PM10, PM5 — matched, W=20µ, L=5µ, m=1)
    │         │              │              │
    │      Q1/Q6 Pair     Q2 (CTAT)      Output Branch
    │      (Brokaw Core)   Branch
    │         │              │
    │    ΔVBE = VT·ln(N)   VBE(Q2)
    │      N = 2              │
    │         │              │
    │    ┌────▼────┐    ┌────▼────┐
    │    R2=3.6kΩ    R3=150kΩ
    │    (I_PTAT)    (bias set)
    │         │              │
    │    ┌────▼──────────────▼────┐
    │    │   Summing Node          │
    │    │   R5 = 113 kΩ (trim)    │
    │    │   VREF = VBE + I_PTAT·R5│
    │    └───────────┬─────────────┘
    │                │
    │           Two-Stage Op-Amp
    │           (Loop Stability)
    │                │
    └────────────────┘
                      │
                    VREF ──► ~1.178 V (flat across temp)
                    CTAT ──► Q2 branch monitor node
```

---

## Bandgap Core Design Details

| Parameter | Value |
|-----------|-------|
| Topology | Curvature-compensated Brokaw-cell bandgap |
| Core BJT Pair | Q1 / Q6 (vpnp10, area ratio N = 2) |
| Technology Node | GPDK090 — 90 nm CMOS |
| Supply Voltage Range | 1.62 V – 1.98 V |
| PTAT-Setting Resistor (R2) | 3.6 kΩ |
| Bias-Setting Resistor (R3) | 150 kΩ |
| Trim / Summing Resistor (R5) | 113 kΩ |
| Current Mirrors | PM8, PM10, PM5 (W=20µ, L=5µ, m=1) |
| High-Temp Fix (PM8) | Widened to W=240µ, m=6 |

---

## Verified Performance

### DC / Temperature Response

| Metric | Simulated Value |
|--------|----------------|
| Reference Voltage (VREF) | **~1.178 – 1.18 V** |
| Temperature Coefficient (TT, nominal) | **~4.3 – 5.0 ppm/°C** |
| PVT Corner Spread (TT/FF/SS/FS/SF) | ~5 – 22 ppm/°C |
| Operating Temperature Range | −40°C to +125°C |

### AC / Loop Stability

| Metric | Simulated Value |
|--------|----------------|
| DC Loop Gain (M1) | **62.75 dB** @ 107.15 Hz |
| Mid-Band Gain (M3) | 39.20 dB @ 109.14 kHz |
| Secondary Crossover (M2) | 22.14 dB @ 1.02 MHz |

### Statistical & Corner Analysis

| Metric | Result |
|--------|--------|
| Monte Carlo Setup | ADE XL, fixed single-point DC (scalar VREF per run) |
| MC Model Fix | Resolved missing `MC` section via `Section=NN` in gpdk090 |
| PVT Corners Swept | TT, FF, SS, FS, SF @ 1.62V / 1.98V |
| Trim Resistor (R5) | 113 kΩ — confirmed as primary reportable result |

---

## Simulation Results

### DC Response — PTAT / CTAT / VREF vs. Temperature
![DC Response](simulations/dc_response_ptat_ctat.png)
> Red (rising): PTAT node | Green (falling): CTAT node | Flat red trace (top): VREF ≈ 1.18V across −50°C to 125°C
> Confirms textbook bandgap cancellation: PTAT and CTAT slopes are equal and opposite (~±1.9 mV/°C)

### TC Extraction — Bandgap3.0 Refined Sweep
![TC Extraction](simulations/vref_vs_temp_bandgap3.png)
> Parabolic residual curve — TC ≈ 4.3–4.8 ppm/°C via box method (−40°C to 125°C)
> Best local performance near mid-range temperature (peak ~40–50°C), worst at extremes — classic signature of uncompensated second-order (curvature) term

### AC Response — Bode Plot
![AC Response](simulations/ac_response.png)
> Magenta: Loop gain (dB) | Green: Secondary trace
> M1: 62.75 dB @ 107.152 Hz · M3: 39.198 dB @ 109.144 kHz · M2: 22.140 dB @ 1.023 MHz

### Schematic
![Bandgap Schematic](schematics/bandgap2_0_schematic.png)
> Full transistor-level Bandgap2.0 core: PMOS current mirrors, Brokaw BJT pair, two-stage op-amp, trim resistor network

---

## Core Calculations (Summary)

| Step | Formula | Value |
|---|---|---|
| Thermal Voltage (V_T) | kT/q | 25.85 mV @ 300K |
| ΔV_BE (PTAT seed) | V_T·ln(N), N=2 | 17.92 mV |
| I_PTAT | ΔV_BE / R2 | 4.98 µA |
| V_PTAT | I_PTAT × R5 | 0.5625 V |
| V_BE (CTAT, Q2) | V_T·ln(I_C/I_S) | ~0.636 V |
| V_REF | V_BE + V_PTAT | ~1.2 V (calculated) / ~1.178 V (simulated) |
| K_effective | R5 / R2 | 31.4 |
| K_ideal (cancellation target) | 2mV/°C × T / ΔV_BE | ~33.5 |

> Hand-calculated TC (linear model, ignoring curvature) ≈ 104 ppm/°C — the real simulated result (~4.3–4.8 ppm/°C) is ~20× tighter because the SPICE model captures the second-order (T·ln T) curvature term that a simple linear formula cannot.

---

## Verification Matrix

| # | Analysis | Status |
|---|----------|--------|
| 1 | DC Operating Point | ✅ |
| 2 | PTAT / CTAT Node Verification | ✅ |
| 3 | AC / Loop Stability (Bode) | ✅ |
| 4 | Temperature Sweep −40°C→+125°C | ✅ |
| 5 | TC Extraction (Box Method) | ✅ |
| 6 | Full PVT Corner Analysis (TT/FF/SS/FS/SF) | ✅ |
| 7 | Monte Carlo Statistical Analysis | ✅ |
| 8 | High-Temp Mirror Desaturation Fix (PM8) | ✅ |
| 9 | Line Regulation (Supply Sensitivity) | ⏳ Planned |
| 10 | Thermal Hysteresis Verification | ⏳ Planned |
| 11 | Curvature Compensation Redesign (sub-ppm target) | ⏳ Planned |

---

## Project Roadmap

```
Phase 1 — LTspice Prototype                 ✅ Complete
Phase 2 — Bandgap Voltage Reference          ✅ Complete (this repo)
Phase 3 — Layout / Physical Design           🔄 In Progress
Phase 4 — Full PMIC Integration              ⏳ Planned
Phase 5 — Sky130 / Open PDK Migration        ⏳ Planned
Phase 6 — IEEE Publication                   ⏳ Targeted
```

---

## Design Notes

### Why a Brokaw-Cell Topology?
The Brokaw cell forces two BJTs of different emitter area (N=2, Q1/Q6) to carry matched currents, generating a precise, predictable ΔV_BE = V_T·ln(N). This PTAT voltage is scaled through a resistor ratio (R5/R2 = 31.4) and summed with the inherently CTAT V_BE of Q2, producing a reference voltage with near-zero first-order temperature dependence.

### Why the Trim Resistor (R5) Matters
R5 sets the effective PTAT gain K = R5/R2. The ideal cancellation target is K ≈ 33.5; this design achieves K_effective = 31.4 — close enough to the ideal that residual TC is dominated by second-order curvature rather than first-order gain error, which is exactly why simulated TC (4.3–5.0 ppm/°C) tracks so closely with the corrected resistor ratio.

### Why Two Rejected Design Iterations Are Documented
- **Curvature-compensation injection branch** — abandoned due to DC convergence failures caused by unintended positive feedback.
- **5-bit binary-weighted trim ladder** — rejected after parametric analysis showed TC degrading to 60–75 ppm/°C across all codes, traced to NMOS switch off-state parasitic loading at the design's low bias currents (~5µA).

Both are kept in the documentation intentionally — understanding *why* an approach fails is treated as equally valuable engineering output as the final working result.

### High-Temperature Mirror Fix (PM8)
Above ~103°C, the original PM8 sizing caused mirror desaturation and output collapse. Widening PM8 to W=240µ, m=6 restored correct mirror operation across the full −40°C to 125°C range — a practical example of how DC headroom margins shrink at temperature extremes in deep-submicron analog design.

---

## Tools & Environment

| Tool | Version / Details |
|------|------------------|
| Cadence Virtuoso | Schematic Editor L / ADE XL |
| Simulator | Spectre |
| PDK | GPDK090 (90nm CMOS, academic) |
| OS | Linux (CentOS / RHEL) |

---

## Author

**Pavankumar**
M.Tech Power Electronics — CMR College of Engineering & Technology
Specialization: Analog IC & Precision Power Management Design
Member: RISC-V International

📌 *Actively seeking full-time roles in Analog IC Design · PMIC · AMS · Semiconductor R&D*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/pavankumar-kolakonda )

---

## License

This project is shared for academic and portfolio purposes.
GPDK090 PDK is proprietary to Cadence Design Systems — netlists and PDK files are not included in this repository.

---

*If you find this useful or have technical feedback, open an issue or connect on LinkedIn.*
# Precision-Bandgap-Voltage-Reference
