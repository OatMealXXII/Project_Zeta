# IoT Carrier Board for Raspberry Pi Pico 2 / 2W (Project Zeta) - Rev 1.1

A rugged, modular IoT carrier board designed for Raspberry Pi Pico 2 and Pico 2W microcontrollers deployed in harsh outdoor, telemetry, and edge-computing environments. This board integrates primary surge protection, reverse-polarity defense, isolated grounding, and sensor interface expansion.

---

## Architecture & Design Highlights

- **Modular Carrier Architecture:** Separates power handling, field protection, and physical wiring from the core logic board, enabling hot-swap servicing and low maintenance overhead.
- **Hardware-First Resilience (KISS Principle):** Built entirely with robust discrete semiconductors and passive protection networks, eliminating fragile active supervisory ICs and maintaining zero quiescent current drain.
- **Sub-Layer High-Energy Routing:** High-current surge paths and gas discharge loops are isolated on the bottom layer to eliminate transient EMI coupling into sensitive microcontroller logic.
- **Dual-Domain Ground Separation:** Features a dedicated `*Earth ⏚` path isolated from logic/signal GND, specifically routed to safely divert atmospheric lightning surges and external ESD directly into structural grounding.

---

## Power & Surge Protection Subsystem


```

[External Surge / Lightning ESD]
│
▼
[GDT (GD1)] ────────────────────────► *Earth ⏚ (Chassis / Field Ground)
│
[DC Input / 1S Li-ion Battery]                  │ (Isolated Domain)
│                                   ▼
▼                              [Earth Rod]
[SS14 Schottky (D1)] ───► (Reverse-Polarity Protection)
│
├───────────────► [TVS Diode (D2)] ──► Signal GND (Overvoltage Clamping)
│
├───────────────► [100µF Bulk Cap]  ──► Signal GND (Inrush & Transient Reservoir)
├───────────────► [100nF Ceramic]   ──► Signal GND (High-Frequency Decoupling)
│
▼
[Regulated VCC Bus / 3V3_EXT Rail to Pico & Sensor Interfaces]

```

### Component Specifications & Engineering Justification

| Designator | Component | Specification / Package | Engineering Function |
| :--- | :--- | :--- | :--- |
| **`GD1`** | **Gas Discharge Tube (GDT)** | 2-Electrode Axial (Spark-over: ~90V–230V) | **Primary surge barrier.** Shunts high-energy transient spikes (kA surge capacity) directly to earth ground without burning the PCB traces. |
| **`D1`, `D3`** | **Schottky Barrier Diode** | SS14 (1A / 40V, SMA / DO-214AC) | Blocks reverse-polarity voltage connection with ultra-low forward voltage drop ($V_F \approx 0.35\text{V}$) on input buses. |
| **`D2`, `D4`** | **TVS / Clamping Diode** | SMAJ5.0A (SMA / DO-214AC) | Suppresses residual fast transients and clamps overvoltage to safe logic thresholds within nanosecond response times. |
| **`C1`, `C3`** | **Electrolytic Capacitor** | 100µF (Radial, Low ESR) | Absorbs input voltage dips and supplies peak burst current during Wi-Fi / LoRa transmission cycles. |
| **`C2`, `C4`** | **Ceramic Capacitor** | 100nF (Radial / Multilayer) | Suppresses high-frequency switching noise adjacent to power distribution headers. |
| **`J1`, `J2`** | **Female Pin Headers** | 1x20 Pin 2.54mm Pitch | Socket interface for Raspberry Pi Pico 2 / Pico 2W. |

---

## Power Subsystem & Battery Management

- **Supported Input Topology:** 1S Li-ion / LiFePO4 battery pack (1S1P, 1S2P, 1S4P) or 5V DC step-down source.
- **Operating Input Range:** **1.8V to 5.5V DC** (Strictly single-cell architecture; **do NOT connect 2S, 3S, or 4S packs** directly without external regulation).
- **External BMS Coordination:** Designed to interface with an external 1S Lithium BMS module handling cell balancing, overcharge (4.2V cut-off), and deep over-discharge protection.
- **Dedicated Sensor Power Bus (`3V3_EXT`):** Independent power tap engineered to prevent high-draw peripheral sensors from loading or resetting the internal Pico voltage regulator.

---

## Commissioning & Verification Guide (Smoke Test)

### Step 1: Cold Test & Continuity Inspection (Unpowered)
1. Verify silkscreen polarity symbols (`+`, `-`, and `*Earth ⏚`) across all terminal blocks.
2. Check resistance with a digital multimeter between `VCC` and `GND` across all power rails.
   - *Pass Criteria:* High impedance / open circuit ($>1\text{ M}\Omega$).
3. Verify that `*Earth ⏚` is completely isolated from system `GND` on the multimeter.
4. Test diodes `D1`–`D4` in Diode Check mode to ensure correct cathode/anode orientation.

### Step 2: First Power-Up (Current-Limited Test)
1. Set a bench power supply to **3.8V DC** with the current limit restricted to **50mA**.
2. Connect leads to the power terminal blocks (without the Raspberry Pi Pico mounted).
3. Measure voltage rails at pin sockets: verify clean DC voltage across `VCC`, `3V3_EXT`, and ground buses.
   - *Pass Criteria:* Quiescent current remains $<1\text{mA}$; no thermal dissipation across protection diodes.

### Step 3: Full-Load Runtime Validation
1. Power down and securely seat the Raspberry Pi Pico 2 / 2W into headers `J1` and `J2`.
2. Increase the power supply current limit to **500mA**.
3. Flash edge telemetry firmware and verify Wi-Fi/LoRa transmission stability under active load.

---

![Render Board](https://github.com/OatMealXXII/Project_Zeta/blob/master/Project_Zeta.png)

## Roadmap

- [x] **Rev 1.0 (Prototype):** Initial 2-layer modular layout; verified discrete protection logic and basic terminal block layout.
- [x] **Rev 1.1 (Current Production):**
  - Added dedicated Primary Surge Stage via Gas Discharge Tube (**GD1**) paired with isolated **`*Earth ⏚`** terminal block.
  - Symmetrical dual-rail topology with auxiliary high-current sensor power (**`3V3_EXT`**).
  - Optimized footprint clearances (eliminated SMD diode soldering crowding from Rev 1.0).
  - Strict 45° trace angle routing across both layers to eliminate acid traps and maintain signal integrity.
- [ ] **Rev 2.0 (Planned):** 4-layer impedance-controlled stackup with dedicated inner ground and power planes (L2/L3), integrated MIFA antenna, and expanded energy-harvesting interfaces.
