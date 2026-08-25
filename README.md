# EV Battery Charging Control using MATLAB/Simulink

## 📌 Overview

This project presents the design and simulation of a **lithium-ion EV battery charging control system** using **MATLAB/Simulink and Simscape Electrical**.

The model demonstrates a simplified **Constant Current (CC) charging strategy with voltage-based cutoff control**. The battery is initially charged at a constant current of **10 A** while its terminal voltage and State of Charge (SOC) are continuously monitored.

When the battery terminal voltage reaches the defined maximum charging voltage of **4.20 V**, the control logic switches the charging current to **0 A**. After the charging current is removed, the battery voltage settles to approximately **4.10 V**, representing its open-circuit steady-state voltage.

A **relay with hysteresis** is used to control the charging current and prevent unwanted rapid switching around the voltage threshold.

---

## 🎯 Objectives

* Model a lithium-ion battery using Simscape Electrical.
* Implement a constant-current battery charging system.
* Measure battery terminal voltage and charging current.
* Monitor battery State of Charge (SOC).
* Develop a closed-loop voltage-based charging controller.
* Automatically stop charging when the battery reaches **4.20 V**.
* Use relay hysteresis to provide stable switching.
* Understand the basic feedback-control principle used in EV battery charging systems.
* Handle the algebraic loop present in the feedback control path.

---

## ⚡ Charging Strategy

The model uses a simplified **constant-current charging with voltage cutoff** approach.

### 1. Constant Current Charging

At the beginning of the simulation, the battery starts at **20% SOC**.

The controlled current source supplies approximately **10 A** to the battery.

During this stage:

* Initial SOC = **20%**
* Charging current = **10 A**
* Battery voltage gradually increases.
* The voltage sensor continuously measures the battery terminal voltage.
* The measured voltage is fed back to the control logic.

Because of the direction of the controlled current source in the model, the current sensor displays the charging current as approximately **-10 A**. The negative sign represents the chosen reference direction of the current sensor and does **not** mean that the battery is discharging.

### 2. Voltage Cutoff

As charging continues, the battery voltage increases until it reaches the maximum charging voltage of:

**4.20 V**

At approximately:

**t ≈ 280 seconds**

the battery reaches the voltage cutoff condition.

The relay then changes its output, causing the controlled current source to switch from the charging state to:

**0 A**

This stops further charging and prevents the battery voltage from continuously increasing.

### 3. Post-Cutoff Behavior

After the charging current is switched OFF:

* Charging current drops to **0 A**.
* Battery voltage falls slightly from **4.20 V**.
* The terminal voltage settles at approximately **4.10 V**.
* SOC reaches approximately **93%**.

The voltage settling occurs because the battery is no longer being actively charged and its terminal voltage moves toward its open-circuit steady-state value.

> **Note:** This is a simplified educational charging model. A practical EV charging system would normally use dedicated current and voltage control loops together with a Battery Management System (BMS).

---

## 🛠️ Tools and Technologies

* **MATLAB**
* **Simulink**
* **Simscape Electrical**
* Lithium-ion battery modeling
* Feedback control
* Relay-based hysteresis control
* Voltage and current sensing

---

## 🧩 Model Architecture

The Simulink/Simscape model consists of the following major components:

### Battery (Table-Based)

Represents the lithium-ion battery and its electrical behavior. The battery starts at approximately **20% SOC**.

### Controlled Current Source

Provides the charging current to the battery.

The current source is configured with its current arrow pointing **upward**, so a positive control command produces the required charging direction.

### Voltage Sensor

Measures the battery terminal voltage and sends the physical signal to the control system.

### Current Sensor

Measures the charging current for monitoring and visualization.

### PS-Simulink Converter

Converts the physical signal from the Simscape voltage/current sensors into a Simulink signal that can be processed by the control logic.

### Relay

Acts as the main voltage-based charging controller. It switches the charging current between the charging state and zero-current state according to the battery voltage.

### Simulink-PS Converter

Converts the relay's Simulink output back into a physical signal that controls the Simscape controlled current source.

### Electrical Reference

Provides the electrical reference/ground for the Simscape electrical network.

### Solver Configuration

Provides the required solver configuration for the Simscape physical network.

### Memory Block

Introduces a delay into the feedback path to help break the algebraic loop between the battery measurement and controlled current source.

### Scope

Displays the main simulation results:

* Battery voltage
* Charging current
* State of Charge (SOC)

---

## 🔄 Control Flow

```text
              ┌──────────────────────┐
              │       Battery        │
              └──────────┬───────────┘
                         │
                         ▼
                  Voltage Sensor
                         │
                         ▼
                PS-Simulink Converter
                         │
                         ▼
                  Relay Controller
                  with Hysteresis
                         │
                         ▼
                 Simulink-PS Converter
                         │
                         ▼
              Controlled Current Source
                         │
                         ▼
                      Battery
```

This forms a **closed-loop voltage feedback system**.

The battery voltage is continuously measured and used to determine whether the charging current should remain ON or be switched OFF.

---

# ⚙️ Updated Control Parameters

| Parameter                  |    Value |
| -------------------------- | -------: |
| Initial SOC                |      20% |
| Maximum Battery Voltage    |   4.20 V |
| Charging Current           |     10 A |
| Charging Current at Cutoff |      0 A |
| Relay Switch-on Point      |   4.20 V |
| Relay Switch-off Point     |   4.00 V |
| Relay Output when ON       |        0 |
| Relay Output when OFF      |       10 |
| Approximate Cutoff Time    |    280 s |
| Final SOC                  |    ≈ 93% |
| Post-Cutoff Voltage        | ≈ 4.10 V |

---

# 🔧 Updated Relay Block Settings

The Relay block is configured to control the charging current based on the battery terminal voltage.

Double-click the **Relay** block and use the following settings:

* **Switch-on point:** `4.20`
* **Switch-off point:** `4.00`
* **Output when on:** `0`
* **Output when off:** `10`

### Why is the output `10` when OFF?

The controlled current source arrow is pointing **upward** in the updated model.

Therefore, a positive control signal of:

**10 A**

drives current into the battery in the charging direction.

When the battery reaches the cutoff voltage, the relay changes its output to:

**0 A**

which stops the charging current.

---

# 🔁 Relay Hysteresis

The relay uses two voltage thresholds:

* **Switch-on point = 4.20 V**
* **Switch-off point = 4.00 V**

This creates a **0.20 V hysteresis band**.

The hysteresis prevents the controller from rapidly changing states when the battery voltage is close to the switching threshold.

Without hysteresis, small numerical fluctuations in the measured battery voltage could cause repeated ON/OFF transitions, commonly referred to as **chattering**.

The relay therefore provides more stable switching behavior.

---

# 🔧 Algebraic Loop Handling

During model development, an **algebraic loop** was observed in the feedback path.

The problem occurs because:

```text
Battery Voltage
      ↓
Controller
      ↓
Current Source
      ↓
Battery
      ↓
Battery Voltage
```

creates a direct dependency between the battery measurement and its input.

A **Memory block** was introduced into the feedback path to provide a delayed value.

This breaks the direct algebraic dependency and allows Simulink to solve the feedback system more reliably.

---

# 📊 Simulation Results

The simulation is performed for **1500 seconds**, with the major charging event occurring at approximately **280 seconds**.

The Scope displays three important signals.

## 1. Battery Voltage

The battery terminal voltage initially increases smoothly from approximately **3.7 V**.

As charging continues:

```text
Initial Voltage
      ↓
Gradual Voltage Increase
      ↓
4.20 V Cutoff
      ↓
Charging Current = 0 A
      ↓
Voltage Settles ≈ 4.10 V
```

The voltage reaches approximately **4.20 V** at the cutoff point.

After the charging current is removed, the voltage decreases slightly and settles around **4.10 V**.

This demonstrates the expected transition from the charging terminal voltage to the battery's steady-state open-circuit voltage.

---

## 2. Charging Current

The current waveform shows the charging process clearly.

Initially:

**Current ≈ -10 A**

The negative value is caused by the reference direction of the current sensor.

At approximately:

**t ≈ 280 s**

the battery reaches the voltage cutoff condition.

The controller then switches the charging current to:

**0 A**

Therefore, the current waveform shows a clean transition:

```text
-10 A ─────────────────┐
                       │
                       │  t ≈ 280 s
                       ▼
  0 A  ────────────────┴────────────
```

This confirms that the controller successfully stops charging when the battery reaches the voltage limit.

---

## 3. State of Charge (SOC)

The battery starts at:

**SOC = 0.20 = 20%**

During charging, SOC increases approximately linearly.

At the voltage cutoff:

**SOC ≈ 0.93 = 93%**

After the charging current becomes zero, SOC remains approximately constant.

The simulation therefore demonstrates:

```text
20% SOC
   ↓
Charging at 10 A
   ↓
SOC increases
   ↓
93% SOC
   ↓
4.20 V reached
   ↓
Charging stopped
```

---

# 📈 Final Simulation Behavior

The three Scope signals can be summarized as follows:

| Signal  | Initial Condition | Charging Phase      | Cutoff                    |
| ------- | ----------------- | ------------------- | ------------------------- |
| Voltage | ≈ 3.7 V           | Gradually increases | 4.20 V → settles ≈ 4.10 V |
| Current | -10 A             | ≈ -10 A             | 0 A                       |
| SOC     | 20%               | Increases           | ≈ 93%                     |

### Important Observation

The charging controller successfully detects the battery voltage limit and automatically stops the charging current.

The final simulation demonstrates:

**Voltage:**
3.7 V → 4.20 V → ≈ 4.10 V

**Current:**
-10 A → 0 A

**SOC:**
20% → ≈ 93%

**Cutoff time:**
≈ 280 seconds

---

# 🧠 Key Learning Outcomes

Through this project, I gained practical experience with:

* Lithium-ion battery modeling
* EV battery charging systems
* Constant-current charging
* Voltage-based charging cutoff
* Closed-loop feedback control
* Simscape Electrical components
* Battery voltage and current sensing
* Simulink-PS and PS-Simulink signal conversion
* Relay-based hysteresis control
* Algebraic loop handling
* MATLAB/Simulink debugging
* Simulation result interpretation

The project helped me understand how battery charging can be implemented as a **feedback-control problem**, where the battery's voltage is continuously monitored and used to control the charging current.

---

# 🚗 Applications

The concepts demonstrated in this project are relevant to:

* Electric Vehicle (EV) charging systems
* Battery Management Systems (BMS)
* EV charging stations
* Battery energy storage systems
* Battery charging controllers
* DC charging infrastructure
* Renewable energy storage systems

The model can be further extended to include a complete EV battery pack, dedicated current control, temperature monitoring, SOC estimation, and advanced BMS protection functions.

---

# 🔮 Future Improvements

The current model can be extended by implementing:

* True CC-CV charging with a dedicated current-control loop
* Battery temperature monitoring
* Over-voltage and under-voltage protection
* Over-current protection
* SOC estimation algorithms
* Battery pack modeling
* BMS integration
* PWM-based DC-DC converter control
* PID-based charging current control
* Constant-voltage regulation after reaching 4.20 V
* Charging efficiency calculation

---

# ✅ Conclusion

This project demonstrates a **closed-loop lithium-ion EV battery charging controller using MATLAB/Simulink and Simscape Electrical**.

The battery starts at **20% SOC** and is charged with approximately **10 A**. The battery voltage gradually increases until it reaches the **4.20 V cutoff voltage at approximately 280 seconds**.

At the cutoff point, the relay changes the control signal so that the charging current becomes **0 A**. The battery SOC reaches approximately **93%**, while the battery voltage subsequently settles around **4.10 V**.

The use of a **relay with hysteresis** provides stable voltage-based switching, while the **Memory block** helps resolve the algebraic loop in the feedback path.

Overall, the project provides practical experience in **battery modeling, feedback control, Simscape Electrical, relay-based control, and MATLAB/Simulink simulation**, forming a foundation for developing more advanced EV charging and BMS systems.
