# EV Battery Charging Control using MATLAB/Simulink

## 📌 Overview

This project presents the design and simulation of a **lithium-ion EV battery charging control system** using **MATLAB/Simulink and Simscape Electrical**.

The model demonstrates the **Constant Current–Constant Voltage (CC-CV)** charging concept. The battery is initially charged with a constant current, while its terminal voltage is continuously monitored. When the battery voltage approaches the maximum charging voltage, the control logic changes the charging state to prevent overcharging.

The project uses a **closed-loop voltage feedback system** with relay-based hysteresis to achieve stable switching during the charging process.

---

## 🎯 Objectives

* Model a lithium-ion battery cell using Simscape Electrical.
* Implement a basic CC-CV charging strategy.
* Measure battery voltage and charging current.
* Develop a closed-loop charging control system.
* Control the charging current based on battery voltage.
* Prevent the battery voltage from exceeding the defined limit.
* Reduce switching chattering using hysteresis control.
* Understand the basic control principles used in EV battery charging systems.

---

## ⚡ CC-CV Charging Strategy

The charging process is divided into two main stages.

### 1. Constant Current (CC) Mode

During the initial stage, the battery is charged using a fixed current.

* **Charging current:** 10 A
* **Initial SOC:** 20%
* Battery voltage is below the maximum voltage limit.
* The controller maintains the charging current while the battery voltage is below the defined threshold.

### 2. Constant Voltage (CV) Mode

As the battery voltage approaches its maximum charging voltage, the controller changes the charging command.

* **Maximum battery voltage:** 4.2 V
* Charging current is reduced or switched OFF when the voltage reaches the defined limit.
* This prevents the battery from being continuously charged beyond its voltage limit.

> **Note:** This project uses a simplified voltage-based control strategy to demonstrate the CC-CV concept. A practical EV charger would normally use a dedicated current-control loop together with a Battery Management System (BMS).

---

## 🛠️ Tools and Technologies

* **MATLAB**
* **Simulink**
* **Simscape Electrical**
* Battery modeling
* Feedback control
* Relay-based hysteresis control

---

## 🧩 Model Architecture

The Simulink/Simscape model consists of the following major components:

* **Battery (Table-Based):** Represents the lithium-ion battery cell and its electrical behavior.
* **Controlled Current Source:** Supplies the charging current to the battery.
* **Voltage Sensor:** Measures the battery terminal voltage and provides feedback.
* **Current Sensor:** Measures the charging current for monitoring.
* **Electrical Reference:** Provides the electrical ground/reference for the Simscape circuit.
* **Solver Configuration:** Provides the required configuration for the Simscape electrical network.
* **PS-Simulink Converter:** Converts Simscape physical signals into Simulink signals.
* **Simulink-PS Converter:** Converts Simulink control signals into Simscape physical signals.
* **Relay:** Controls the charging state using voltage thresholds and hysteresis.
* **Memory Block:** Helps break the algebraic loop in the feedback path.
* **Scope:** Displays battery voltage and charging current during simulation.

---

## 🔄 How the Model Works

The battery is initially set to **20% SOC** and is connected to a controlled current source.

The battery voltage is continuously measured using a voltage sensor. This physical signal is converted into a Simulink signal and passed to the control logic.

The controller compares the measured battery voltage with the defined voltage thresholds. Based on the result, the relay determines whether the charging current should remain ON or be switched OFF.

### Control Flow

```text
Battery
   ↓
Voltage Sensor
   ↓
PS-Simulink Converter
   ↓
Voltage Control Logic
   ↓
Relay with Hysteresis
   ↓
Simulink-PS Converter
   ↓
Controlled Current Source
   ↓
Battery
```

This creates a **closed-loop control system**, where the battery voltage is continuously measured and used to determine the charging command.

---

## ⚙️ Control Parameters

* **Initial SOC:** 20%
* **Maximum Battery Voltage:** 4.2 V
* **Charging Current:** 10 A
* **Charging Current at Voltage Limit:** 0 A
* **Relay ON Point:** 4.19 V
* **Relay OFF Point:** 4.20 V

These parameters were selected to make the charging behavior and controller response clearly observable during simulation.

---

## 🔁 Switching Problem and Hysteresis

During the initial implementation, the charging logic was created using a **Relational Operator and Switch**.

Although this approach works away from the voltage threshold, it can cause rapid ON/OFF switching when the battery voltage is close to **4.2 V**.

This behavior is known as **chattering**.

To improve the switching behavior, a **Relay block with hysteresis** is used.

### Relay Settings

* **ON Point:** 4.19 V
* **OFF Point:** 4.20 V

The difference between these two thresholds creates a **hysteresis band**, preventing the controller from repeatedly switching when the battery voltage fluctuates around the limit.

---

## 🔧 Algebraic Loop Handling

During development, an **algebraic loop warning** was observed in the feedback path.

The issue occurred because the control signal was directly dependent on the battery measurement while the battery input was simultaneously being controlled by that feedback signal.

A **Memory block** was introduced into the feedback path to provide a delayed signal and break the direct algebraic dependency.

This helps Simulink solve the feedback system more reliably during simulation.

---

## 📊 Expected Simulation Results

### Battery Voltage

* The battery voltage gradually increases as charging continues.
* Initially, the voltage remains below the maximum limit of **4.2 V**.
* As the SOC increases, the battery voltage approaches the charging limit.
* When the voltage reaches the defined threshold, the control logic changes the charging state.

### Charging Current

* During the initial charging stage, the charging current remains approximately **10 A**.
* As the battery voltage approaches the maximum limit, the controller responds according to the defined control logic.
* When the voltage reaches the charging limit, the charging current is reduced or switched to **0 A**.
* The relay hysteresis prevents rapid ON/OFF switching around the voltage threshold.

---

## 📈 Results to Include

For the final GitHub version, the following simulation plots/screenshots can be added:

1. **Complete Simulink Model**
2. **Battery Voltage vs. Time**
3. **Charging Current vs. Time**
4. **SOC vs. Time**
5. **Relay/Control Signal vs. Time**

---

## 🧠 Key Learning Outcomes

Through this project, I gained practical experience with:

* Lithium-ion battery modeling
* EV battery charging concepts
* CC-CV charging strategy
* Closed-loop feedback control
* Simscape Electrical components
* Voltage and current sensing
* Simulink-PS and PS-Simulink signal conversion
* Relay-based hysteresis control
* Algebraic loop handling
* MATLAB/Simulink debugging and simulation

The project helped me understand how battery charging can be implemented as a **feedback-based control problem** rather than simply applying a fixed charging current.

---

## 🚗 Applications

The concepts demonstrated in this project are applicable to:

* Electric Vehicle (EV) charging systems
* Battery Management Systems (BMS)
* EV charging stations
* Battery energy storage systems
* Battery charging controllers
* DC charging infrastructure
* Renewable energy storage systems

The model can be further extended from a single battery cell to a complete EV battery pack and a more advanced charging system.

---

## ✅ Conclusion

This project demonstrates the basic implementation of an **EV lithium-ion battery charging controller using MATLAB/Simulink**.

A battery model, controlled current source, voltage and current sensors, and feedback-based relay control are combined to demonstrate the charging process.

The addition of **hysteresis control** improves the stability of the switching operation near the battery voltage limit, while the Memory block helps resolve the algebraic loop in the feedback path.

Overall, this project provides a foundation for developing more advanced **EV charging controllers and Battery Management System (BMS)** models.

