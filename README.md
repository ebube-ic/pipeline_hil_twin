\# Industrial Pipeline HIL Digital Twin \& Fault-Tolerant Automation



An end-to-end Hardware-in-the-Loop (HIL) and Software-in-the-Loop (SIL) Digital Twin environment simulating a 1 km, 300 mm diameter crude oil pipeline facility. The system bridges a physics-based hydraulic plant simulation with an external IEC 61131-3 virtual PLC runtime, an analytical redundancy engine for fault detection, and an enterprise SCADA supervisory layer.



\---



\## System Architecture



The project is structured across four decoupled industrial layers:



1\. \*\*Tier 1: Physical Plant Simulation (Simscape Fluids / SIL Core)\*\*

&#x20;  A discretized fluid transmission line capturing real-world pipeline dynamics: fluid inertia, viscous friction losses, acoustic pressure transients (water hammer), and liquid column separation.



2\. \*\*Tier 2: Controller Runtime (CODESYS IEC 61131-3)\*\*

&#x20;  A virtual PLC running Structured Text (ST) implementing Model-Based Control for Variable Frequency Drive (VFD) pump speed regulation, surge suppression, and automated safety interlocks.



3\. \*\*Tier 3: Analytical Redundancy \& Fault-Tolerant Control (FTC)\*\*

&#x20;  Dual real-time failure mitigation modules:

&#x20;  \* Sensor Drift / Freezing Detection: Cross-validation of pressure transmitter channels against hydraulic gradient models.

&#x20;  \* Pipeline Breach Localization: Dynamic mass-balance calculations identifying sudden mass flow divergence.



4\. \*\*Tier 4: Supervisory \& Historian Layer (Ignition SCADA + PostgreSQL)\*\*

&#x20;  High-performance Process Flow Diagrams (PFD) presenting live line pressures, localized hydraulic gradients, pump motor metrics, trip event logging, and audit tracking.



\---



\## Week 1 Milestone: Tier 1 Plant Model Construction \& Hydro-Transient Validation



The goal for Week 1 was building and numerically validating the physical hydraulic transmission plant to ensure physical accuracy and solver stability during rapid transient shocks.



\### Pipeline \& Fluid Specifications



| Parameter | Value | Engineering Context |

| :--- | :--- | :--- |

| \*\*Line Length ($L$)\*\* | 1000 m | Transmission sector distance |

| \*\*Internal Diameter ($D$)\*\* | 0.3 m (300 mm) | Mainline crude line bore |

| \*\*Fluid Density ($\\rho$)\*\* | 850 kg/m³ | Light-to-medium crude oil |

| \*\*Isothermal Bulk Modulus ($\\beta$)\*\* | 1.5 GPa | Fluid elasticity matrix |

| \*\*Acoustic Wave Speed ($a$)\*\* | 1328.4 m/s | Speed of sound in medium ($a = \\sqrt{\\beta/\\rho}$) |

| \*\*Centrifugal Pump Capacity\*\* | 15,000 lpm @ 200 m head | 500 kW mainline crude pump |

| \*\*Nominal Operating Velocity\*\* | 3.54 m/s | Steady-state delivery flow |



\---



\### Dynamic Model Implementation



The pipeline plant was developed in Simscape Fluids and tuned to eliminate numerical chattering, algebraic loop singularities, and non-physical vacuum pressures:



!\[Simscape Pipeline Plant Diagram](docs/images/Screenshot%201.png)



\* \*\*Actuator Sizing \& Input Filtering\*\*: The emergency shut-off ball valve bore was matched directly to the 300 mm pipe diameter ($0.0707\\text{ m}^2$) with a $1\\times 10^{-6}\\text{ m}^2$ seat leakage area to eliminate hard boundary discontinuities. A 50 ms first-order input filter was applied to the valve command to model rapid pneumatic actuator travel while maintaining DAE solver integrity.

\* \*\*Controlled VFD Ramp\*\*: The centrifugal pump uses a 2-second speed ramp from 0 to 1800 RPM, establishing smooth steady-state delivery head without launching premature startup transients.

\* \*\*Fluid Cavitation Dynamics\*\*: A 0.2% entrained air fraction was integrated into the isothermal liquid matrix. This cushions the rapid rarefaction phase during column separation, bounding low-pressure troughs at physical vapor limits ($0\\text{ to } -1\\text{ bar}$ gauge) instead of artificial negative tensiles.



\---



\### Transient Scope Analysis



A rapid emergency shutdown (ESD) closure was commanded at $t = 5.0\\text{ s}$ to validate water hammer propagation across the 1 km line:



!\[Pipeline Transient Pressure Waveforms](docs/images/Screenshot%202.png)



The simulated response matches analytical Joukowsky transient theory:



$$\\Delta P = \\rho \\cdot a \\cdot \\Delta v = 850 \\times 1328.4 \\times 3.537 \\approx 39.9\\text{ bar}$$



Adding the 20 bar steady-state operating head yields a theoretical peak surge of $\\approx 60\\text{ bar}$, identical to the 60 bar pressure crest recorded at the valve face. The round-trip acoustic reflection period ($2L/a$) holds at 1.5 seconds per cycle before secondary cavitation rebound occurs at $t = 8.8\\text{ s}$, executing with zero solver warnings.



\---



\## Roadmap



\- \[x] \*\*Week 1\*\*: Design, size, and validate the dynamic pipeline physical plant in Simscape Fluids.

\- \[ ] \*\*Week 2\*\*: Expose plant process variables over Modbus TCP / OPC UA for real-time external communications.

\- \[ ] \*\*Week 3\*\*: Implement the CODESYS IEC 61131-3 virtual PLC runtime and Structured Text VFD pump control logic.

\- \[ ] \*\*Week 4\*\*: Develop analytical redundancy algorithms for sensor drift detection and mass-balance breach isolation.

\- \[ ] \*\*Week 5\*\*: Deploy Ignition SCADA dashboard with PostgreSQL event historian integration.

