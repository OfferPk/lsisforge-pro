# LunaNet Signal-In-Space Recommended Standard (LSIS-AFS) Specification

## Technical Reference, Augmented Forward Signal Architecture & Selenocentric PNT

### 1\. Executive Summary & Purpose

The **LunaNet Signal-In-Space Recommended Standard — Augmented Forward Signal (LSIS-AFS)**, codified under **NASA TP-20230012812** and standardized across NASA, ESA, and JAXA, establishes the normative signal structure, frequency plan, modulation schemes, and telemetry formats for lunar positioning, navigation, and timing (PNT) and communications forward broadcasts.

As international space programs deploy infrastructure to support the **Artemis Program**, **Lunar Gateway**, **ESA Moonlight**, and commercial lunar landers, traditional Earth-based tracking (e.g. DSN) becomes constrained by line-of-sight occultation on the lunar farside and polar regions. LunaNet establishes an autonomous lunar satellite constellation providing continuous forward-link positioning signals directly to surface and orbital users.

---

### 2\. Signal-In-Space Radio Frequency (RF) Architecture

#### 2.1 Frequency Bands & Power Allocation

LSIS-AFS Volume A establishes two primary forward broadcast allocations:

- **Lunar S-Band Forward**:  
  - Allocated Spectrum: \$2483.5\\text{ MHz} \- 2500.0\\text{ MHz}\$ (Bandwidth \= \$16.5\\text{ MHz}\$)  
  - Center Frequency: \$f\_c \= 2491.75\\text{ MHz}\$  
- **Lunar Ka-Band Forward**:  
  - Allocated Spectrum: \$22.55\\text{ GHz} \- 23.55\\text{ GHz}\$ (Bandwidth \= \$1.0\\text{ GHz}\$)  
  - Center Frequency: \$f\_c \= 23.050\\text{ GHz}\$

#### 2.2 Dual-Component Signal Structure

To simultaneously support robust code tracking and low-latency navigation telemetry, LSIS-AFS defines an in-phase (I) and quadrature-phase (Q) composite signal:

- **Data Component (In-Phase)**:  
  - Spreading Modulation: \$\\text{BPSK}(1)\$  
  - Chipping Rate: \$R\_c \= 1.023\\text{ Mchip/s}\$ (\$1 \\times f\_0\$)  
  - Telemetry Data Rate: \$250\\text{ bps}\$ (\$500\\text{ sps}\$ encoded with Rate 1/2 Convolutional FEC, \$K=7\$, polynomials \$G\_1=171\_8, G\_2=133\_8\$)  
  - Primary PRN Code Length: \$10,230\\text{ chips}\$ (\$10\\text{ ms}\$ epoch)  
- **Pilot Component (Quadrature-Phase)**:  
  - Spreading Modulation: \$\\text{BPSK}(5)\$  
  - Chipping Rate: \$R\_c \= 5.115\\text{ Mchip/s}\$ (\$5 \\times f\_0\$)  
  - Data Modulation: Data-less pure carrier/ranging pilot  
  - Primary PRN Code Length: \$51,150\\text{ chips}\$ (\$10\\text{ ms}\$ epoch)  
  - Power Sharing: \$50% / 50%\$ nominal split (\$3\\text{ dB}\$ relative balance)

---

### 3\. Lunar Navigation Broadcast Message (LNBM) Structure

The navigation message is formatted into repeating multi-tier subframe cycles:

\+-----------------------------------------------------------------------------------+

\|               LunaNet Navigation Broadcast Message (LNBM) Frame                   \|

\+-----------------------------------------------------------------------------------+

\| \[Sync Preamble: 0x8B (8 bits)\] \| \[PRN ID (6 bits)\] \| \[Time-of-Week (17 bits)\]    \|

\+-----------------------------------------------------------------------------------+

  \|

  \+--\> Subframe 1: Clock Parameters (Epoch LTS, a0, a1, a2, Tgd, Health Status)

  \|

  \+--\> Subframe 2: Selenocentric Ephemeris (MCMF\_PA, A, e, i0, Omega0, omega, M0, rates)

  \|

  \+--\> Subframe 3: Constellation Almanac & Health (Orbiter health mask, reduced ephemeris)

  \|

  \+--\> Subframe 4: Augmentation & Safety Services (Lunar SAR alerts, space weather, RTK)

  \|

  \+--\> Parity: 24-bit CRC-24Q (Polynomial 0x1864CFB)

#### 3.1 Coordinate Reference Frames

- **MCMF (Moon-Centered Moon-Fixed)**: Lunar Principal Axis (PA) or Mean Earth/Polar Axis (ME) frame.  
- **Lunar Time System (LTS)**: Synchronized atomic time scale steered to Coordinated Universal Time (UTC) with explicit relativistic offset modeling.

---

### 4\. Pseudorange & Doppler Mathematical Formulations

#### 4.1 Pseudorange Observation Equation

The pseudorange \$\\rho\$ measured by a lunar surface user is:

\$\$\\rho \= \|\\vec{R}*{\\text{sat}}(t \- \\tau) \- \\vec{R}*{\\text{user}}(t)\| \+ c \\cdot (\\delta t\_{\\text{user}} \- \\delta t\_{\\text{sat}}) \+ \\Delta\_{\\text{rel}} \+ \\Delta\_{\\text{mult}} \+ \\epsilon\$\$

Where:

- \$\\vec{R}*{\\text{sat}}, \\vec{R}*{\\text{user}}\$: Geocentric / Selenocentric 3D position vectors in MCMF.  
- \$\\delta t\_{\\text{sat}} \= a\_0 \+ a\_1(t \- t\_{oc}) \+ a\_2(t \- t\_{oc})^2\$: Satellite clock polynomial offset.  
- \$\\Delta\_{\\text{rel}}\$: Relativistic correction (\$\\approx \-\\frac{2\\sqrt{\\mu A}}{c^2} e \\sin E\$).

#### 4.2 Radial Velocity & Doppler Frequency Shift

For relative velocity vector \$\\vec{v}*{\\text{rel}} \= \\vec{v}*{\\text{sat}} \- \\vec{v}\_{\\text{user}}\$ and unit line-of-sight vector \$\\vec{u}\$:

\$\$f\_d \= \-\\frac{\\vec{v}\_{\\text{rel}} \\cdot \\vec{u}}{c} \\cdot f\_c\$\$