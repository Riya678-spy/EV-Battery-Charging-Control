# EV Battery Charging Control (CC-CV) — MATLAB/Simulink

## Overview
This project implements a **Constant Current – Constant Voltage (CC-CV) charging controller** for an EV battery cell, modeled and simulated in MATLAB/Simulink using the Simscape Electrical (Battery) library. The model simulates the electrical behavior of a lithium-ion battery cell being charged by a controlled current source, with feedback-based switching between charging modes.

## Objective
To design and simulate a closed-loop charging control system that:
- Delivers a fixed **charging current** to the battery while its terminal voltage is below a safe threshold (**CC mode**).
- Reduces/limits the charging current once the battery voltage reaches its maximum rated value, protecting the cell from overcharging (**CV mode**).

This is directly relevant to EV charging infrastructure design, battery management systems (BMS), and charge-point control logic — applicable to Jio-bp's EV charging network use case.

## Tools & Environment
- MATLAB / Simulink
- Simscape Electrical (Battery, Sensors, Sources, Utilities libraries)

## Model Architecture
| Block | Purpose |
|---|---|
| Battery (Table-Based) | Simscape Li-ion cell model with SOC-based open-circuit voltage table |
| Electrical Reference | Circuit ground |
| Controlled Current Source | Injects charging current into the battery, driven by control logic |
| Voltage Sensor | Measures battery terminal voltage (feedback signal) |
| Current Sensor | Measures charging current (for monitoring) |
| PS-Simulink / Simulink-PS Converters | Bridge Simscape physical signals and Simulink signals |
| Solver Configuration | Required Simscape network solver settings |
| Control Logic (Relational Operator + Switch, being upgraded to a Relay block) | Compares battery voltage against V_max and switches the charging current between `I_charge` and `I_stop` accordingly |
| Scope | Displays battery voltage and current over the simulation run |

## Control Logic
- **V_max** set to 4.2 V (typical full-charge voltage for a single Li-ion cell, matching the battery block's data table).
- **I_charge** = 10 A (constant current during CC phase).
- **I_stop** = 0 A (current cut when voltage reaches V_max).
- Initial battery **State of Charge (SOC)** set to 20%, so the charging transition is observable during simulation.

## Current Status
The core physical circuit (battery, sensors, current source, ground, solver) is fully built and wired. The control loop (voltage feedback → switching logic → current source) is functional and produces the expected qualitative CC-CV behavior: current stays high while voltage is below threshold, and cuts back once voltage approaches the limit.

**Known issue / next step:** The present switching logic (Relational Operator + Switch) causes rapid oscillation ("chattering") near the voltage threshold, since there's no hysteresis band. The fix in progress is to replace this with a **Relay block** (built-in hysteresis: on-point 4.19 V, off-point 4.20 V) to produce a smooth, stable CC-to-CV transition. A Memory block has been added in the feedback path to resolve an algebraic loop warning from the solver.

## Planned Next Steps
1. Complete the Relay-based hysteresis control swap.
2. Re-verify current sensor polarity/sign.
3. Re-run simulation and confirm a clean CC-CV voltage/current profile (flat current during CC, tapering current with voltage plateau during CV).
4. Tune V_max, I_charge, and hysteresis band against realistic EV cell datasheet values.
5. Add SOC vs. time plot for a complete charging picture.

## Relevance to Jio-bp Application
This project demonstrates hands-on understanding of:
- EV battery charging behavior and CC-CV charge algorithms
- Simscape/Simulink-based power system modeling
- Closed-loop control design and debugging (including solver-level issues like algebraic loops)

---
*Note: This README reflects the project's state as of the current working session. Update this document once the Relay-based control fix is verified and final results are captured, before adding this project to a resume.*
