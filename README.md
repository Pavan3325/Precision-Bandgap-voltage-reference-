# Precision Bandgap Voltage Reference (Bandgap2.0)

**Curvature-Compensated Brokaw-Cell Bandgap Reference with PMOS LDO and Thermal Shutdown**

Designed and verified in Cadence Virtuoso on the gpdk090 (90nm) CMOS process, as part of an M.Tech research project in Precision Power Management IC Design.

---

## 📌 Overview

This repository documents the design, simulation, and verification of a temperature-compensated Bandgap Voltage Reference (BGR) targeted at high-precision power management applications. The design integrates a Brokaw-cell bandgap core, a PMOS-based LDO, and a thermal shutdown block, forming a foundational element of a larger Precision Power Management Platform.

This project is part of the broader thesis work:

> *"Design and Analysis of a Precision Power Management Platform Featuring a Sub-ppm/°C Voltage Reference, Ultra-Low-Noise LDO, and Adaptive Thermal Regulation for Multi-Sector Applications."*

---

## 🎯 Key Results

| Metric | Result |
|---|---|
| Temperature Coefficient (TT, nominal) | ~4.32 – 5.01 ppm/°C |
| Operating Temperature Range | -40°C to 125°C |
| PVT Corner Spread (TT/FF/SS/FS/SF) | ~5 to 22 ppm/°C |
| Process Node | gpdk090 (90nm CMOS) |
| Supply Range Tested | 1.62V – 1.98V |
| Trim Resistor (R5) | 113 kΩ |

---

## 🛠️ Engineering Highlights

- **High-temperature mirror desaturation fix** — resolved output collapse above ~103°C by widening the critical PMOS current mirror (PM8: W=240µ, m=6)
- **Full PVT corner analysis** across TT/FF/SS/FS/SF corners at 1.62V and 1.98V supply
- **AC stability verification** — closed-loop phase margin and gain characterized via internal op-amp AC response
- **Monte Carlo analysis** set up in ADE XL (process + mismatch), including resolution of a missing `MC` model section in gpdk090 (fixed via `Section=NN`)
- **Documented design failures** for transparency and learning:
  - Curvature-compensation injection branch — abandoned due to DC convergence failures from unintended positive feedback
  - 5-bit binary-weighted trim ladder — rejected after parametric analysis showed TC degradation to 60–75 ppm/°C, traced to NMOS switch off-state parasitic loading at low bias currents

---

## 📂 Repository Structure

```
├── schematics/
│   ├── bandgap2_0_schematic.png        # Full bandgap + LDO + thermal shutdown schematic
├── simulation_results/
│   ├── dc_response_ptat_ctat.png        # PTAT/CTAT vs temperature sweep
│   ├── ac_response_phase_margin.png     # Loop gain & phase response
│   ├── vref_vs_temp.png                 # Reference voltage stability plot
├── docs/
│   ├── design_report.pdf                # Full IEEE-format project report
│   ├── pvt_corner_tables.md             # PVT corner analysis summary
│   ├── design_equations.md              # Key bandgap design equations
├── README.md
```

---

## 🔬 Verification Methodology

1. **DC Analysis** — PTAT/CTAT current generation and reference voltage summation
2. **Temperature Sweep** — TC extraction using the box method across -40°C to 125°C
3. **PVT Corner Analysis** — full corner sweep across process, voltage, and temperature
4. **AC Analysis** — loop stability, gain, and phase margin of internal op-amp
---

## 📈 Roadmap

- [ ] Dropout voltage characterization
- [ ] Thermal hysteresis verification
- [ ] VOUT PVT sweep (full system-level)
- [ ] System-level Monte Carlo integration
- [ ] Curvature compensation redesign (targeting sub-ppm/°C)
- [ ] Integration with ultra-low-noise LDO regulator

---

## 🧑‍💻 About This Project

This work was completed as part of an M.Tech in Power Electronics, with a focus on analog and mixed-signal IC design — specifically bandgap references, PMIC architectures, and LDO regulators.

**Interested in connecting?**
I'm actively seeking full-time and internship opportunities in Analog/Mixed-Signal IC Design. Feel free to open an issue, reach out via LinkedIn, or connect directly.

---

## 📜 License

This repository is shared for educational and portfolio purposes. Please reach out before reusing schematics or data for commercial purposes.
