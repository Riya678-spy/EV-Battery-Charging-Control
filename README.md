# EV Battery Charging Control using MATLAB/Simulink

## Overview

This project focuses on the design and simulation of a basic **EV battery charging control system** using MATLAB/Simulink and Simscape Electrical.

The main idea is to model how a lithium-ion battery can be charged safely using the Constant Current–Constant Voltage (CC-CV) charging method, which is commonly used for lithium-ion batteries in electric vehicles and other energy-storage applications.

The model starts by charging the battery with a constant current. As the battery voltage approaches its maximum allowable value, the control system changes its behavior and limits the charging current to prevent overcharging.

The project also includes a feedback control loop that continuously monitors the battery voltage and makes the charging decision automatically.

## Objective

The main objectives of this project are:

* To model a lithium-ion battery cell in Simscape Electrical.
* To implement a basic CC-CV charging strategy.
* To monitor battery terminal voltage using a voltage sensor.
* To use feedback to control the charging current.
* To prevent the battery voltage from exceeding its maximum charging limit.
* To study the transition between constant-current charging and voltage-limited charging.
* To understand the basic control logic used in EV battery charging systems.

## Why CC-CV Charging?

Lithium-ion batteries cannot simply be charged with a constant current until they are completely full. The charging current needs to be controlled as the battery voltage increases.

The CC-CV method is therefore divided into two stages:

### 1. Constant Current (CC) Mode

During the initial stage, the battery is charged using a fixed current.

In this model:

* Charging current =10 A
* Initial SOC = 20%
* Battery voltage is below the maximum voltage limit.

The controller allows the charging current to remain at 10 A while the battery voltage is below the defined threshold.

### 2. Constant Voltage (CV) Mode

When the battery voltage approaches its maximum charging voltage, the controller changes the charging behavior.

For this project:

* Maximum battery voltage =4.2 V
* Charging current is reduced/cut off when the voltage reaches the limit.
This prevents the battery from being continuously charged beyond its safe voltage level.

Note: The present model uses a simplified voltage-based control strategy for demonstrating the CC-CV concept. A practical EV battery charger would use a more detailed current regulation loop and a battery management system.

##Model Architecture

The complete model consists of the following major components:
Battery (Table-Based): Represents the lithium-ion battery cell and its electrical behavior.
Controlled Current Source: Supplies the required charging current to the battery.
Voltage Sensor: Measures the battery terminal voltage and provides feedback to the controller.
Current Sensor: Measures the charging current for monitoring and analysis.
Electrical Reference: Provides the electrical ground/reference for the Simscape circuit.
Solver Configuration: Defines the required settings for simulating the Simscape electrical network.
PS-Simulink Converter: Converts physical signals from Simscape into Simulink signals for control processing.
Simulink-PS Converter: Converts Simulink control signals into physical signals used by Simscape blocks.
Relay / Control Logic: Compares the battery voltage with the set threshold and controls the charging current.
Memory Block: Helps break the algebraic loop in the feedback path and improves simulation stability.
Scope: Displays the battery voltage and charging current during the simulation.

## How the Model Works

-The battery is initially set to approximately 20% State of Charge (SOC).
-A controlled current source supplies the charging current to the battery. At the same time, the battery terminal voltage is continuously measured using a voltage sensor.
-The measured voltage is sent to the Simulink control system through a PS-Simulink Converter.
-The controller compares the measured battery voltage with the predefined voltage limit.

The basic control sequence is:

Battery
   │
   ▼
Voltage Sensor
   │
   ▼
PS-Simulink Converter
   │
   ▼
Voltage Control Logic
   │
   ▼
Relay / Switching Control
   │
   ▼
Simulink-PS Converter
   │
   ▼
Controlled Current Source
   │
   ▼
Battery
This creates a closed-loop system because the battery voltage is measured and then used to determine the charging command.

##Control Parameters

The main simulation parameters used in the model are:
Initial SOC: 20%
Maximum Battery Voltage: 4.2 V
Charging Current: 10 A
Charging Current at Voltage Limit: 0 A
Relay ON Point: 4.19 V
Relay OFF Point: 4.20 V

## Switching Problem and Hysteresis

During the initial implementation, the charging logic was created using a Relational Operator and Switch.
This approach works when the battery voltage is far from the threshold. However, when the voltage gets very close to 4.2 V, even a small change in the measured voltage can repeatedly turn the charging current ON and OFF.

## Relay-Based Hysteresis Control

To solve the switching problem, the control logic is being upgraded to a Relay block with hysteresis.
The relay uses two different voltage thresholds instead of a single threshold:
* ON point = 4.19 V
* OFF point = 4.20 V
The small difference between these two thresholds creates a hysteresis band.
This prevents the controller from repeatedly switching when the battery voltage fluctuates around 4.2 V.

## Algebraic Loop Issue

-While developing the feedback control system, an algebraic loop warning was also observed.
-This happened because the control signal depended directly on the battery measurement while the battery input was simultaneously being controlled by that same feedback path.
-A Memory block was introduced into the feedback path to provide a delayed value of the control signal and break the direct algebraic dependency.
This allows Simulink to solve the feedback system more reliably during simulation.

##Expected Simulation Results

The expected behavior of the battery during the charging process is:
Battery Voltage:
The battery voltage gradually increases as the charging process continues.
Initially, the voltage remains below the maximum voltage limit of 4.2 V.
As the SOC increases, the battery terminal voltage approaches 4.2 V.
When the voltage reaches the defined threshold, the control logic changes the charging state.
Charging Current:
During the initial charging stage, the battery receives a constant charging current of approximately 10 A.
As the battery voltage approaches the maximum limit, the controller responds according to the defined control logic.
When the voltage reaches the charging limit, the charging current is reduced or switched to 0 A to prevent further overcharging.
The Relay block with hysteresis helps prevent rapid ON/OFF switching around the voltage threshold.

## Key Learning Outcomes

Through this project, I worked with:
* Lithium-ion battery modeling
* EV battery charging concepts
* CC-CV charging strategy
* Feedback control systems
* Simscape Electrical components
* Physical signal conversion between Simscape and Simulink
* Voltage and current sensing
* Relay-based hysteresis control
* Algebraic loop handling
* MATLAB/Simulink simulation and debugging
The project helped me understand how battery charging can be modeled as a closed-loop control problem rather than simply applying a fixed charging current.

## Applications

The concepts demonstrated in this project are relevant to:
* Electric Vehicle (EV) charging systems
* Battery Management Systems (BMS)
* EV charging stations
* Energy storage systems
* Battery charging controllers
* DC charging infrastructure
* Renewable energy storage
The same basic concepts can be extended to larger battery packs and more advanced EV charging systems.

## Conclusion

This project demonstrates a basic but practical approach to controlling the charging of a lithium-ion battery using MATLAB/Simulink.
By combining a battery model, sensors, a controlled current source, and feedback-based switching logic, the model shows how charging current can be controlled according to the battery's terminal voltage.
The project provides a foundation for developing more advanced EV battery charging and Battery Management System (BMS) models in the future.

