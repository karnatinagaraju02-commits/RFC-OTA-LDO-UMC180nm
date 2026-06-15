# RFC-OTA-LDO-UMC180nm

**M.Tech Thesis — High-Precision LDO using High-Swing RFC OTA in UMC 180nm CMOS | NIT Silchar 2026**

---

## Overview

This repository documents the design and simulation of two Low-Dropout (LDO) voltage regulators using the Recycling Folded Cascode (RFC) OTA as the error amplifier, implemented in UMC 180nm CMOS technology using Cadence Virtuoso/Spectre.

LDO regulators for battery-powered IoT sensor nodes face a fundamental three-way conflict: output voltage precision, loop stability, and quiescent current consumption — all coupled through the error amplifier transconductance and bias current.

This work proposes a **High-Swing RFC OTA with self-biased cascode architecture (Design 2)** and benchmarks it against a conventional RFC OTA baseline (Design 1). The key innovation is a self-biased cascode current mirror that simultaneously improves output voltage swing (~120 mV recovery), provides automatic PVT compensation, and eliminates parasitic bias current overhead — without external trimming.

---

## Specifications

| Parameter | Value |
|---|---|
| Technology | UMC 180nm CMOS (1P6M) |
| Supply Voltage (V_DD) | 1.8 V |
| Regulated Output (V_out) | 1.2 V |
| Reference Voltage (V_ref) | 0.8 V |
| Max Load Current | 50 mA |
| Load Capacitance | 1 µF |
| Feedback Resistors | R1 = 50 kΩ, R2 = 100 kΩ |
| Pass Transistor | W = 2000 µm / L = 1.03 µm (PMOS) |
| OTA Transistor Sizing | Moderate inversion, gm/ID = 17–19 V⁻¹ (Design 2) |
| Simulator | Cadence Spectre (BSIM3v3) |

---

## Design Comparison (Nominal: TT, 1.8V, 27°C, Full Load)

| Parameter | Design 1 (Baseline RFC) | Design 2 (HS-RFC) | Change |
|---|---|---|---|
| DC Loop Gain | 71.95 dB | 68.45 dB | −3.5 dB |
| GBW (Full Load) | 7.61 MHz | 460 kHz | −16× |
| Phase Margin (Full Load) | 61.49° | 86.77° | +43% |
| Phase Margin (Light Load) | — | 42.72° | ⚠ Weak |
| Load Regulation | 19.75 µV/mA | 7.98 µV/mA | −60% |
| PSRR @ 1 kHz | −41.57 dB | −46.62 dB | +5.1 dB |
| Quiescent Current | 66.32 µA | 72.46 µA | +9.2% |
| Settling Time | 0.73 µs | 8.06 µs | +11× |
| Undershoot (load step) | 29 mV | 67 mV | — |
| Current Efficiency | 99.87% | 99.86% | ≈same |

> ⚠ **Light load phase margin (42.72°) is a known limitation.** At light load the dominant pole shifts and loop gain rises to 101.1 dB, reducing phase margin. Output capacitor selection is critical.

---

## Key Technical Contributions

**1. Self-biased high-swing cascode biasing for RFC OTA-based LDO**
Cascode gate voltages self-generated from the circuit's own bias network using diode-connected devices. Recovers ~120 mV output swing, provides automatic PVT tracking, eliminates parasitic bias current from external resistors.

**2. gm/ID sizing procedure for RFC OTA in LDO context**
Systematic transistor sizing in moderate inversion (gm/ID = 17–19 V⁻¹) using UMC 180nm PDK lookup tables. Achieves 40–50% reduction in drain current at same transconductance target vs. strong inversion.

**3. 27-point PVT validation**
Full factorial analysis: 3 process corners (SS, TT, FF) × 3 supply voltages (1.62, 1.80, 1.98 V) × 3 temperatures (−40°C, 27°C, 125°C). Phase margin variation across all 27 points: **0.50°**.

**4. Quantitative speed-stability trade-off characterisation**
Two RFC OTA variants benchmarked systematically across gain, bandwidth, phase margin, load regulation, and PSRR.

---

## PVT Robustness (Design 2, Full Load)

### Process Corners — 27°C, 1.8V

| Corner | GBW | Phase Margin | Load Regulation |
|---|---|---|---|
| TT | 460 kHz | 86.77° | 7.09 µV/mA |
| SS | 430 kHz | 86.52° | 4.73 µV/mA |
| FF | 464 kHz | 87.02° | 14.73 µV/mA |

**Total PM variation: 0.50°**

### Supply Voltage — TT, 27°C

| V_DD | Phase Margin (FL) | PSRR @ 1kHz |
|---|---|---|
| 1.62 V | 86.67° | −38.71 dB |
| 1.80 V | 86.77° | −46.62 dB |
| 1.98 V | 86.82° | −48.71 dB |

**Total PM variation: 0.15°**

### Temperature — TT, 1.8V

| Temperature | GBW | Phase Margin (FL) | Load Regulation |
|---|---|---|---|
| −40°C | 521 kHz | 86.63° | 3.28 µV/mA |
| 27°C | 460 kHz | 86.77° | 7.09 µV/mA |
| 125°C | 320 kHz | 86.66° | 48.42 µV/mA |

> ⚠ **Load regulation degrades to 48.42 µV/mA at 125°C** due to DC gain reduction at high temperature.

---

## Comparison with State-of-the-Art (180nm, 1.8V)

| Parameter | [1] Rincon'98 | [4] Wang'21 | [5] Tan'25 | This Work |
|---|---|---|---|---|
| Tech Node | 350nm | 180nm | 180nm | **180nm** |
| Max Load | 50 mA | 100 mA | 30 mA | **50 mA** |
| I_q (µA) | — | 95 | 7 | **72.5** |
| Load Reg. | — | 72 µV/mA | 207 µV/mA | **7.98 µV/mA** |
| Phase Margin | 55° | 61° | 65° | **86.77°** |
| PVT Validation | No | No | No | **27-point** |

---

## Schematics

### LDO System Architecture
![LDO System Architecture](schematics/LDO%20SCHEMATIC%20FINAL1.jpg)

### Design 1 — Baseline RFC OTA
![Design 1](schematics/BASELINE%20OTA_final.jpg)

### Design 2 — Proposed High-Swing RFC OTA
![Design 2](schematics/PROPOSED%20OTA%20FINAL.jpg)

---

## Simulation Results

### Frequency Response (Gain, GBW, Phase Margin) — Design 1 vs Design 2
![Frequency Response](results/gain%20%26GBW%20%26PM%20of%20design1%20%26%202.jpeg)

### PSRR — Design 1 vs Design 2
![PSRR](results/psrr%20of%20both%20designs%20.png)

### Transient Response — Load Step
![Transient](results/transient_final_v7(1).png)

---

## PVT Results

### Stability — Process Corners (Full Load)
![Process Full Load](PVTresults/pvt%20process/phase@full@process.png)

### Stability — Process Corners (Light Load)
![Process Light Load](PVTresults/pvt%20process/phase@light@process.png)

### PSRR — Process Corners
![PSRR Process](PVTresults/pvt%20process/psrr@process.png)

### Stability — Temperature Variation (Full Load)
![Temp Full Load](PVTresults/pvt%20temp/phase@full@temp.png)

### Stability — Temperature Variation (Light Load)
![Temp Light Load](PVTresults/pvt%20temp/phase@light@temp.png)

### PSRR — Temperature Variation
![PSRR Temp](PVTresults/pvt%20temp/psrr@temp.png)

### Stability — Supply Voltage (Full Load)
![Voltage Full Load](PVTresults/PVT%20VOLTAGE/phasemargin%20@full@voltage.png)

### Stability — Supply Voltage (Light Load)
![Voltage Light Load](PVTresults/PVT%20VOLTAGE/phasemargin@light@voltage.png)

### PSRR — Supply Voltage
![PSRR Voltage](PVTresults/PVT%20VOLTAGE/psrr@voltage.png)

---

## Simulation Data (CSV)

Raw Cadence Spectre export data provided for result verification.

### Nominal Results (`results/csv results/`)
| File | Contents |
|---|---|
| `conventional ota gain.csv` | Loop gain, phase vs frequency — Design 1 |
| `proposed ota gain.csv` | Loop gain, phase vs frequency — Design 2 |
| `psrr of conventional ota final.csv` | PSRR vs frequency — Design 1 |
| `psrr of proposed ota.csv` | PSRR vs frequency — Design 2 |
| `transient response of conventional ota.csv` | Vout transient — Design 1 |
| `transient response of proposed ota.csv` | Vout transient — Design 2 |

### PVT Data (`PVTresults/`)
**Process corners** (`pvt process/`): STB full/light load and transient at SS/TT/FF; PSRR at all corners

**Temperature sweep** (`pvt temp/`): STB full/light load and transient at −40°C / 27°C / 125°C; PSRR at all temperatures

**Supply voltage sweep** (`PVT VOLTAGE/`): PSRR, STB full/light load at 1.62V / 1.8V / 1.98V

---

## Limitations

- **Simulation only** — schematic-level Cadence Spectre. No layout, post-layout extraction, or fabrication.
- **Ideal voltage reference** — V_ref = 0.8 V ideal source. Real bandgap adds temperature drift and noise.
- **Proprietary PDK** — UMC 180nm CMOS PDK not distributable. Netlists not included.
- **Light load stability** — PM drops to 42.72° at light load. Output capacitor selection is critical.
- **High-temperature load regulation** — degrades to 48.42 µV/mA at 125°C.
- **No startup circuit** — bias assumed correctly established at power-on.
- **Model-to-hardware gap** — 5–10% errors typical at schematic level; post-layout parasitics will further affect PM and PSRR.

---

## Repository Structure

```
RFC-OTA-LDO-UMC180nm/
├── README.md
├── schematics/
│   ├── LDO SCHEMATIC FINAL1.jpg
│   ├── BASELINE OTA_final.jpg
│   └── PROPOSED OTA FINAL.jpg
├── results/
│   ├── gain &GBW &PM of design1 & 2.jpeg
│   ├── psrr of both designs .png
│   ├── transient_final_v7(1).png
│   └── csv results/
│       ├── conventional ota gain.csv
│       ├── proposed ota gain.csv
│       ├── psrr of conventional ota final.csv
│       ├── psrr of proposed ota.csv
│       ├── transient response of conventional ota.csv
│       └── transient response of proposed ota.csv
└── PVTresults/
    ├── pvt process/
    │   ├── phase@full@process.png
    │   ├── phase@light@process.png
    │   ├── psrr@process.png
    │   └── pvt process csv/ (CSV files)
    ├── pvt temp/
    │   ├── phase@full@temp.png
    │   ├── phase@light@temp.png
    │   ├── psrr@temp.png
    │   └── pvt temp csv/ (CSV files)
    └── PVT VOLTAGE/
        ├── phasemargin @full@voltage.png
        ├── phasemargin@light@voltage.png
        ├── psrr@voltage.png
        └── pvt csv voltage/ (CSV files)
```

---

## Publication

Karnati Nagaraju, Saurav Chanda, and Dr. Bijit Choudhuri, "A High-Stability, Ultra-Precise Low-Dropout Regulator using RFC OTA for Low-Power IoT Applications," in *Proc. International Conference on Emerging Trends in Technology, Engineering, Computing and Networking (ET2ECN 2026)*, SVNIT Surat, April 2026. Published in **Springer Lecture Notes in Electrical Engineering (LNEE)**. [Presented]

---

## Author

**Karnati Nagaraju**
M.Tech, Microelectronics & VLSI Design
National Institute of Technology Silchar (2024–2026)
CGPA: 8.48/10

Supervisor: Dr. Bijit Choudhuri, Asst. Professor, ECE Dept., NIT Silchar
Co-author: Saurav Chanda, Analog Design Engineer, Intel

---

## Tools

| Tool | Purpose |
|---|---|
| Cadence Virtuoso | Schematic entry |
| Cadence Spectre (BSIM3v3) | Circuit simulation |
| UMC 180nm CMOS PDK (1P6M) | Device models |
| Cadence ADE XL | PVT parametric sweeps |
