
# Ultra Sonic Distance Print Using VSD FPGA

## 1. Introduction

This document details the design and implementation of a **Ultra Sonic Distance Print Using VSD FPGA** using an **VSD FPGA**. The system measures distance via the **HC-SR04 ultrasonic sensor** and transmits the calculated distance over **UART** to a serial terminal.

---

## 2. System Overview

### Main Components:
- **12 MHz Internal Oscillator**
- **Clock Divider & Refresher Module** – Controls measurement timing (e.g., every 250 ms)
- **Ultrasonic Module (`hc_sr04.v`)** – Triggers the sensor, counts echo pulse width, calculates distance
- **UART Transmitter (`uart_tx_8n1.v`)** – Sends ASCII-formatted distance at **9600 baud**
- **Optional RGB LED** – Visual feedback
- **USB–Serial Adapter** – Connects to PC terminal

---

## 3. Ultrasonic Sensor Basics

### 3.1 Principle of Operation:
The **HC-SR04** works by:
- Emitting a **40 kHz sound wave pulse**
- Waiting for the reflected wave (echo) to return
- Measuring the **duration of the echo pulse** to calculate distance

### 3.2 Distance Formula:
```
Distance (cm) = (Echo Pulse Duration (μs) * 0.0343) / 2
```
Where:
- Speed of sound ≈ **343 m/s**
- Division by 2 accounts for the to-and-fro journey of the wave

### 3.3 Conversion Formula for FPGA (12 MHz clock):
Each clock tick = 1 / 12 MHz = 83.33 ns
```
Distance (cm) = (Clock Count * 34300) / (2 * 12000000)
```


## 4. Annotated Code Listings & Highlights

### 4.1 UART Transmitter (8N1)
```verilog
// State machine: IDLE -> START -> DATA -> STOP
always @(posedge clk_baud) begin
    case(state)
        IDLE: if (senddata) begin state <= START; tx <= 0; end
        START: ...
        DATA: ...
        STOP: begin tx <= 1; state <= IDLE; end
    endcase
end
```
- **Baud Rate**: 9600
- **Data Format**: 8 data bits, no parity, 1 stop bit

### 4.2 Ultrasonic Sensor Module
```verilog
always @(posedge clk) begin
    case (state)
        IDLE:    if (measure) state <= TRIGGER;
        TRIGGER: if (trig_done) state <= WAIT;
        WAIT:    if (echo) state <= COUNTECHO;
        COUNTECHO:
            if (!echo) begin
                distance_cm <= (echo_count * 34300) / (2 * 12000000);
                state <= IDLE;
            end
    endcase
end
```
#### Key Calculations:
```verilog
distance_cm = (echo_count * 34300) / (2 * 12000000);
```

### 4.3 Refresher Timer (250ms pulse)
```verilog
always @(posedge clk) begin
    if (counter == 3000000) begin
        measure <= 1;
        counter <= 0;
    end else begin
        measure <= 0;
        counter <= counter + 1;
    end
end
```

### 4.4 ASCII Conversion and UART Transmission
```verilog
// Convert numeric distance to ASCII characters '0' - '9'
ascii_data = (distance_cm / 10000) % 10 + 8'd48;  // Extract each digit and add ASCII offset
```

---

## 5. Testing Procedures

- **Connections:** TRIG, ECHO, 5V, GND, UART TX
- **Baud Rate:** 9600
- **Expected Terminal Output:** "10 cm" for ~10 cm

---


## 6. Synthesis & Programming

### 6.1 Build Flow
```bash
git clone https://github.com/gowthamnow/VSD-MAZE-ROBOT
cd 
make build  # Yosys + nextpnr + icepack
```

### 6.2 Flash FPGA
```bash
sudo make flash  # Upload top.bin via iceprog
```

### 6.3 Serial Terminal Monitoring
```bash
sudo make terminal  # Monitor at 9600 baud
```

---

## 7. Conclusion

✅ **Complete Working System**
- Accurate distance measurement with **HC-SR04**
- Data transmitted as ASCII via **UART**
- Real-time updates every 250ms
- Scalable for additional sensors or enhancements (error handling, variable baud rates)

---

## 8. References & Acknowledgments
- [VSDSquadron FPGA Board Datasheet](https://www.vlsisystemdesign.com/vsdsquadronfm/)
- [Yosys Open Synthesis Suite](https://yosyshq.net/yosys/)
- [nextpnr Place-and-Route Tool](https://github.com/YosysHQ/nextpnr)
- [Icestorm FPGA Toolchain](https://github.com/YosysHQ/icestorm)
