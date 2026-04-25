# TECHNICAL SPECIFICATION: GROUNDGUARD™ PD-TRIGGER & ISOLATED PROTOCOL PROXY
**Document ID:** GG-PDT-2026-REV04
**Author:** Juho Artturi Hemminki
**Classification:** Proprietary Technology / Defensive Publication
**Status:** Available for Licensing

---

## 1. ARCHITECTURAL OVERVIEW
The GroundGuard™ PD-Trigger represents a paradigm shift in USB-C Power Delivery (PD). Unlike traditional sink triggers that act as simple endpoints, GroundGuard™ functions as a **Galvanically Anchored Protocol Proxy**.

It exists in the "Electrical Buffer Zone" between the Source (Provider) and the Sink (Consumer), executing a hardware-level "Air-Gap" for the Configuration Channel (CC) while maintaining high-current VBUS throughput.

---

## 2. ELECTRICAL ENGINEERING & POWER DYNAMICS

### 2.1 The Y-Capacitor Leakage Equation
In Class II (un-earthed) SMPS systems, the chassis potential ($V_{ch}$) is governed by the capacitive reactance of the EMI filters. Without GroundGuard™, the floating potential is defined by:

$$V_{ch} = V_{in} \cdot \frac{C_{Y1}}{C_{Y1} + C_{Y2}}$$

Where:
- $V_{in}$ = Input AC Voltage (e.g., 230V)
- $C_{Y1, Y2}$ = Capacitance of Y-capacitors (typically 2.2nF)

GroundGuard™ introduces a parallel low-impedance path ($Z_{PE}$), forcing $V_{ch} \to 0$ relative to Protective Earth (PE). The fault current ($I_f$) follows Ohm’s Law, shunting through the GroundGuard™ busbar:

$$I_f = \frac{V_{ch}}{Z_{PE} + Z_{human}} \approx \frac{V_{ch}}{Z_{PE}} \quad (\text{since } Z_{PE} \ll Z_{human})$$

### 2.2 PD-Trigger Impedance Matching
For the charger to initiate VBUS delivery without digital CC negotiation (Deep Stealth™ Mode), GroundGuard™ emulates the CC-termination constants ($R_d$). The trigger circuit presents a precise resistance to the Source:

$$R_d = 5.1k\Omega \pm 10\%$$

This signals a "Sink Attached" state. To prevent the Source from entering a "Disconnect" state when CC lines are severed, GroundGuard™ utilizes an internal **Hold-Current Circuit** to maintain the Power Delivery Contract.

---

## 3. PROTOCOL PROXY LOGIC (DEEP STEALTH™)

### 3.1 The Handshake Isolation Sequence
The GroundGuard™ PD-Trigger executes a state-machine transition to ensure "Dark Charging":

1. **Detection Phase:** The Source detects $R_d$ on CC1/CC2.
2. **Negotiation Phase:** GroundGuard’s internal PD-Controller (e.g., FUSB302-based or custom ASIC) negotiates the Power Data Object (PDO).
* *Equation for Power Selection:* $P_{max} = V_{sel} \cdot I_{sel}$
3. **Isolation Phase (The Innovation):** Upon "PS_READY" signal, a physical MOSFET-array or high-speed analog switch severs the CC path to the Host.
4. **Reference Anchoring:** The Host-side CC pins are pulled to a stable internal reference to prevent the Host OS from triggering "Incompatible Accessory" errors.

### 3.2 Metadata Scrubbing
By acting as the proxy, GroundGuard™ prevents the exchange of **Unstructured Vendor Defined Messages (UVDM)**. The charger never interacts with the Host’s Unique Identity (UID).

---

## 4. BOM (BILL OF MATERIALS) & COMPONENT SELECTION


| Component | Specification | Function |
| :--- | :--- | :--- |
| **Main Controller** | ARM Cortex-M0+ / STUSB4500 | PD Negotiation & State Machine |
| **Grounding Busbar** | 1.5mm² OFC (Oxygen-Free Copper) | Galvanic Potential Equalization |
| **Isolation Switch** | Low $R_{on}$ Analog Switch (e.g., NX3L) | Physical CC-Line Severance |
| **Protection** | TVS Diode Array + 100nF X7R Cap | ESD & Transient Suppression |
| **Enclosure** | UL94-V0 Fire-Retardant PC | Thermal & Physical Shielding |

---

## 5. SONIC PURGE™: AUDIO FIDELITY ANALYSIS
The impact of GroundGuard™ on analog audio circuits can be measured via Total Harmonic Distortion + Noise (THD+N). By removing the common-mode AC hum ($f_{hum} = 50Hz$), the signal integrity is preserved:

$$SNR_{dB} = 20 \log_{10} \left( \frac{V_{signal}}{V_{noise(PE\_stable)}} \right)$$

GroundGuard™ typically reduces the noise floor by **18dB to 24dB** in un-earthed laptop setups.

---

## 6. INTELLECTUAL PROPERTY DECLARATION & LICENSING

### 6.1 Prior Art Claim
This document establishes **Juho Artturi Hemminki** as the inventor of the *Isolated Protocol Proxy* method for USB-C. This includes the specific sequence of:
1. Independent PD-triggering.
2. Hardware-level CC-line severance post-negotiation.
3. Simultaneous chassis potential equalization through the USB-C Shield.

### 6.2 Licensing Agreement (Standard Terms)
Commercial use of the GroundGuard™ PD-Trigger architecture is governed by the following terms:
- **Licensor:** Juho Artturi Hemminki.
- **Scope:** Global license for manufacturing, marketing, and sub-licensing.
- **Royalties:** Subject to a **Net Sales Percentage** or **Fixed Unit Fee** as per the formal Licensing Contract.
- **Transfer of Know-How:** Includes PCB Gerber files, Firmware source code, and Certification test results (CE/FCC/RoHS).

**Contact for Commercial Handover:**
Juho Artturi Hemminki
Email: [projectflagcarrier@gmail.com]

---
*© 2026 Juho Artturi Hemminki. All rights reserved. GroundGuard™, Deep Stealth™, and Sonic Purge™ are trademarks of the author. Reverse engineering of the Spoofer Circuit for commercial gain without a license is a violation of international IP laws.*
