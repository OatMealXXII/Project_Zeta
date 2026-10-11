# IoT Carrier Board for ESP32 (Project Zeta) - Rev 1.0

A modular, IoT Carrier Board designed for Rasberry Pi Pico 2 or 2W microcontrollers deployed in outdoor and edge computing environments. This board provides robust power distribution, multi-stage electrical protection, and clean sensor I/O interfacing.

---

## Architecture & Design Highlights

* **Modular Carrier Architecture:** Separates power protection and signal distribution from core computing logic, enabling rapid replacement and low maintenance overhead.
* **KISS Principle Hardware:** Built purely on highly reliable passive and discrete semiconductor protection circuits, avoiding fragile active control logic and preserving zero-quiescent-current operation.
* **Integrated Cell & System Safety:** Operates alongside external battery-level protection to create an end-to-end multi-layer defense against physical and electrical hazards.

---

## Power & Protection Subsystem

The input power stage incorporates a multi-tier protection network designed to withstand harsh field operating conditions:

```
[DC Input / 1S Li-ion Battery]
           │
           ▼
    [Polyfuse (0.65A)]  ──── (Overcurrent / Short-Circuit Protection)
           │
           ├────────────► [TVS Diode SMAJ5.0A] ──► GND (Overvoltage Clamping / ESD)
           │
     [SS14 Schottky]    ──── (Reverse Polarity Protection)
           │
           ├────────────► [100µF Bulk Cap]     ──► GND (Inrush Buffering / Transient Storage)
           ├────────────► [100nF Ceramic Cap]  ──► GND (High-Frequency Noise Filtering)
           │
           ▼
    [VCC Distribution / Regulated Bus to MCU & Sensors]

```

### Component Justification & Specifications

| Component | Designator | Specification / Package | Primary Engineering Function |
| --- | --- | --- | --- |
| **Resettable PTC Fuse** | `F1` | RXEF065 (Hold: 0.65A, Trip: ~1.3A) | Interrupts sustained overcurrent and short-circuit conditions without requiring component replacement. |
| **TVS Diode** | `D2` | SMAJ5.0A (SMA / DO-214AC) | Clamps overvoltage spikes, lightning transients, and ESD to ground in nanosecond response times. |
| **Schottky Barrier Diode** | `D1` | SS14 (1A / 40V, SMA / DO-214AC) | Blocks reverse-polarity voltage connection with minimal forward voltage drop ($V_F \approx 0.35\text{V}$). |
| **Electrolytic Capacitor** | `C1` | 100µF 50V (Radial / Low ESR) | Absorbs input ripple and provides reservoir current during Wi-Fi / LoRa transmission bursts. |
| **Ceramic Capacitor** | `C2` | 100nF (2A104J / Metallized Film) | High-frequency noise suppression and decoupling right before system regulation. |

---

## Battery Subsystem

* **Configuration:** 1S4P (1-Series, 4-Parallel) Lithium-ion battery pack. (You can use 3P,2P,1P as you need but it's only support 1S DO NOT USE 2S,3S,4S Battery if you want just control it's in 1.8V-5.5V)
* **Nominal Voltage:** 3.7V (Charging ceiling: 4.2V).
* **Pack Protection:** External 1S 2.5A BMS module providing fundamental cell overcharge, overdischarge, and primary short-circuit isolation.
* **Selectivity Coordination:** The carrier board’s 0.65A Polyfuse trips (~1.3A) before triggering the BMS's 2.5A threshold, isolating downstream carrier faults while preserving continuous battery operation.

---

## Commissioning & Verification (Bring-up Guide)

### 1. Visual & Unpopulated Continuity Inspection

1. Inspect silkscreen polarity markings (`+` and `-`) against schematic footprints.
2. Using a digital multimeter in continuity mode, probe between `VCC` and `GND`.
* *Pass Criteria:* No audible beep / open circuit ($>1\text{ M}\Omega$).



### 2. Protection Stage Population (Cold Test)

1. Solder `F1`, `D1`, `D2`, `C1`, and `C2` onto the PCB.
2. Probe continuity across `VCC` and `GND` again to verify no solder bridging on diode pads.
3. Test `D1` and `D2` in Diode Mode to ensure cathode/anode orientation aligns with footprint markings.

### 3. First Power-Up (Smoke Test)

1. Supply 3.7V–5.0V DC to the board input using a bench power supply with current limiting enabled (set limit to 100mA).
2. Measure DC voltage at the MCU power headers.
* *Pass Criteria:* Voltage reads accurately within expected bounds with negligible quiescent current draw.



---

## Roadmap

* [x] **Rev 1.0 (Current):** 2-Layer modular carrier board prototyping, discrete protection stage validation.
* [ ] **Rev 2.0 (Planned):** 4-Layer stackup optimization featuring a dedicated solid ground plane (L2) and power plane (L3) for enhanced EMI reduction, signal integrity and support more alternative power.
