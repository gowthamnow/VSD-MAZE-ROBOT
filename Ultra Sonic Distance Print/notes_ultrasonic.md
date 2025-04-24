# HC-SR04 Ultrasonic Sensor Module 

This module interfaces with the **HC-SR04 ultrasonic sensor**, which measures distance using sound waves. Below is a **simple breakdown** of how it works, with tables and clear explanations.

---

## **1. How the HC-SR04 Sensor Works**  
The sensor has two main pins:  
- **TRIG** (Trigger) – You send a **10µs pulse** to start measurement.  
- **ECHO** – Goes **high** when the sound is sent and **low** when the echo returns.  

The **time ECHO stays high** is used to calculate distance:  

\[
Distance (cm) = (ECHO_high_time × Speed_of_Sound) / 2
\]

- **Speed of sound** ≈ **343 m/s** (34,300 cm/s)  
- **Divide by 2** because the sound travels to the object and back.  

---

## **2. Module Inputs & Outputs (Table)**  

| **Signal**      | **Direction** | **Purpose** |
|----------------|-------------|-------------|
| `clk`          | Input       | 12 MHz clock (timing) |
| `measure`      | Input       | Starts measurement when pulsed |
| `state`        | Output      | Shows current state (debugging) |
| `ready`        | Output      | High when sensor is idle |
| `echo`         | Input       | Echo signal from sensor |
| `trig`         | Output      | 10µs trigger pulse to sensor |
| `distanceRAW`  | Output      | Raw clock cycles while ECHO was high |
| `distance_cm`  | Output      | Final distance in centimeters |

---

## **3. State Machine (Step-by-Step)**  

The module uses **4 states** to control the measurement process:  

| **State**  | **What Happens** | **TRIG Pin** | **Counter Action** |
|------------|------------------|--------------|-------------------|
| **IDLE**      | Waiting for `measure` signal | Low | No counting |
| **TRIGGER**   | Sending 10µs pulse | High | Counts **120 cycles** (10µs @ 12MHz) |
| **WAIT**      | Waiting for ECHO to rise | Low | Resets `distanceRAW` |
| **COUNTECHO** | Measuring ECHO pulse | Low | Counts cycles until ECHO falls |

### **State Transitions (Flow)**  
1. **IDLE → TRIGGER** when `measure` is pulsed.  
2. **TRIGGER → WAIT** after **10µs** (120 cycles).  
3. **WAIT → COUNTECHO** when `echo` goes high.  
4. **COUNTECHO → IDLE** when `echo` goes low.  

---

## **4. Distance Calculation**  

The sensor measures **how long ECHO stays high** (`distanceRAW` in clock cycles).  

### **Formula Used in Code**  
\[
distance_cm = (distanceRAW × 34300) / (2 × 12,000,000)
\]

- **34300** = Speed of sound (cm/s)  
- **12,000,000** = Clock frequency (12MHz)  
- **Divide by 2** (round-trip time)  

### **Example Calculation**  
If `distanceRAW = 1200` cycles:  
\[
distance_cm = (1200 × 34300)/24,000,000 = 41,160,000/24,000,000 ≈ 17.15 cm
\]

---

## **5. Refresher Module (for Automatic Measurements)**  

The `refresher250ms` module **automatically triggers measurements** every **50ms or 250ms** (adjustable).  

### **How It Works**  
- Counts clock cycles up to **600,000 (50ms)** or **3,000,000 (250ms)**.  
- When the counter resets, it sends a **1-cycle `measure` pulse**.  
- Can be **enabled/disabled** with `en` input.  

| **Setting** | **Cycles** | **Refresh Rate** | Use Case |
|------------|-----------|-----------------|----------|
| 600,000    | 50ms      | 20Hz (fast)     | Real-time tracking |
| 3,000,000  | 250ms     | 4Hz (slow)      | Power-saving mode |

---

## **6. Common Mistakes & Fixes**  

✅ **Problem:** Sensor doesn’t respond.  
🔹 **Fix:** Ensure **TRIG pulse is exactly 10µs** (120 cycles @ 12MHz).  

✅ **Problem:** Wrong distance readings.  
🔹 **Fix:** Check if `clk` is **12MHz**, or adjust the distance formula.  

✅ **Problem:** Sensor locks up (ECHO stays high).  
🔹 **Fix:** Add a **timeout** in `COUNTECHO` state (not in this code).  

✅ **Problem:** Measurements are noisy.  
🔹 **Fix:** Take **multiple readings and average** them.  

---

## **7. Key Takeaways**  
✔ **HC-SR04** measures distance using **sound waves**.  
✔ **10µs TRIG pulse** starts measurement.  
✔ **ECHO high time** determines distance.  
✔ **State machine** controls the process (IDLE → TRIGGER → WAIT → COUNTECHO).  
✔ **Refresher module** automates measurements (50ms or 250ms).  

This module is **simple but complete**—ready to integrate into robotics, obstacle detection, or any distance-sensing project! 🚀
