

### **Module - ultra_sonic_sensor.v**

This Verilog code module interacts with an **HC-SR04 ultrasonic sensor**. The sensor works by sending out an ultrasonic pulse via the **TRIG** pin and listening for the returning echo on the **ECHO** pin. The time it takes for the echo to return is used to calculate the distance to the object.

---

### **State Machine for the HC-SR04 Module**

The state machine controls the flow of actions between triggering the sensor and measuring the echo return. There are **four states** in total:

| **State**    | **Description**                                         |
|--------------|---------------------------------------------------------|
| `IDLE`       | The system waits for a measurement trigger.             |
| `TRIGGER`    | A short pulse (~10 µs) is sent to the **TRIG** pin.     |
| `WAIT`       | The system waits for the **ECHO** pin to go high.      |
| `COUNTECHO`  | The system counts the cycles while the **ECHO** pin is high, storing this count in **distanceRAW**. |

[▶️ Watch Maze Demo](maze2.mp4)


#### State Transitions:

- **From `IDLE`**: Moves to `TRIGGER` on the `measure` signal (when the system is ready).
- **From `TRIGGER`**: Once the pulse duration is complete (approximately 10 µs), it moves to the `WAIT` state.
- **From `WAIT`**: It waits for the **ECHO** pin to rise (start of the echo).
- **From `COUNTECHO`**: When the **ECHO** pin goes low, it moves back to the `IDLE` state after the distance is computed.

---

[▶️ Watch Maze Demo](maze2.mp4)

### **Signal Descriptions**

| **Signal**       | **Direction**  | **Description**                                                   |
|------------------|----------------|-------------------------------------------------------------------|
| `clk`            | Input          | ~12 MHz system clock.                                              |
| `measure`        | Input          | Starts a measurement when high, triggered from another module.    |
| `state`          | Output (2-bit) | Debug signal showing current state of the state machine.          |
| `ready`          | Output         | High in `IDLE`, indicating the system is ready to take a new measurement. |
| `echo`           | Input          | Echo signal from the ultrasonic sensor. Signals when the pulse is reflected. |
| `trig`           | Output         | Sends the trigger signal (~10 µs pulse to start measurement).     |
| `distanceRAW`    | Output         | Raw count of clock cycles while the **ECHO** pin is high.         |
| `distance_cm`    | Output         | The computed distance in centimeters.                             |

---

### **Trigger Pulse Generation**

A 10µs pulse is required to trigger the ultrasonic sensor. The Verilog code implements this pulse by counting clock cycles using a 10-bit counter.

- **Counter Setup**: The `counter` variable increments on each clock cycle.
- **Pulse Duration**: When the counter reaches a predefined value (`ten_us = 120` for ~10 µs at 12 MHz), it triggers the **TRIG** pin.

| **Signal**      | **Action**                                   |
|-----------------|----------------------------------------------|
| `counter`       | Counts clock cycles to generate the trigger pulse (~10 µs). |
| `trig`          | High during the **TRIGGER** state (pulse generated). |

---

### **Distance Measurement and Calculation**

The core of the module is counting the number of clock cycles during which the **ECHO** pin is high. The distance is calculated using the formula:

\[
distance_cm = (distanceRAW * 34300) / (2 * 12000000)
\]

Where:
- `distanceRAW`: The number of clock cycles measured during the echo time.
- `34300`: The speed of sound in cm/s.
- `12000000`: The number of clock cycles in 1 second at a 12 MHz clock.

| **Variable**      | **Description**                                                 |
|-------------------|---------------------------------------------------------------|
| `distanceRAW`     | Counts the number of clock cycles while the **ECHO** pin is high. |
| `distance_cm`     | The calculated distance in centimeters. It uses `distanceRAW` for computation. |

---

### **Refresher Module (Timing Control)**

The refresher module ensures that the ultrasonic sensor is triggered at regular intervals. It generates a pulse (`measure`) at approximately **50ms** (or **250ms** based on the counter setting). This pulse is then used by the **HC-SR04** module to initiate a new measurement.

| **Interval**     | **Clock Cycles (12 MHz)**        | **Duration**          |
|------------------|----------------------------------|-----------------------|
| 50ms             | 600,000                          | 50ms                  |
| 250ms            | 3,000,000                        | 250ms                 |

The `measure` signal is high for one clock cycle when the counter reaches the desired value, triggering a new measurement.

---

### **Operation Summary**

1. **Initial Condition**: 
   - The **HC-SR04 Ultrasonic Sensor** is initially in the `IDLE` state, waiting for the `measure` signal to trigger a new measurement.

2. **Triggering**: 
   - When `measure` is high, the sensor enters the **TRIGGER** state, generating a 10 µs pulse on the **TRIG** pin. This pulse initiates the emission of an ultrasonic wave.
   - The **counter** counts the clock cycles while the pulse is active.

3. **Echo Reception**: 
   - Once the ultrasonic wave is reflected by an object, the **ECHO** pin goes high, signaling the return of the sound wave.
   - The state machine moves into the **WAIT** state, where it stays until the **ECHO** pin rises.

4. **Cycle Counting**: 
   - The **COUNTECHO** state begins when the **ECHO** pin is high.
   - In this state, the `distanceRAW` counter increments on each clock cycle, counting the total time for the echo to return (in clock cycles).

5. **Distance Calculation**: 
   - When the **ECHO** pin goes low, the counter stops, and the total cycles (`distanceRAW`) are used to compute the distance in centimeters.
   - The formula used is:

   \[
   distance_cm = (distanceRAW * 34300) / (2 * 12000000)
   \]

7. **Periodicity**: 
   - The **refresher250ms** module ensures that the `measure` signal is sent periodically (at ~50ms or ~250ms intervals), triggering the sensor again for continuous measurements.

8. **Return to IDLE**: 
   - Once the distance is calculated and the cycle count is recorded, the state machine returns to the `IDLE` state, waiting for the next trigger.

This process ensures continuous measurements with periodic updates based on the ultrasonic sensor’s echo time, enabling real-time distance calculations.

---

### **Table of Parameters and Constants**

| **Parameter**      | **Value/Description**                             |
|--------------------|---------------------------------------------------|
| `ten_us`           | 120 (for a ~10µs pulse duration at 12 MHz clock). |
| `distanceRAW`      | Raw cycle count during the echo pulse.           |
| `distance_cm`      | Final computed distance in centimeters.          |


  



