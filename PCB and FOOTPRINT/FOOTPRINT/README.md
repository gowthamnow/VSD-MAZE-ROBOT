# 🧠 VSD Squadron FSM FPGA – Altium Symbol & Footprint Library

This repository provides a complete, **custom-built Altium Designer component** for the **VSD Squadron FSM FPGA**, including schematic symbol and PCB footprint. The library is designed with precision according to the official pinout and layout specifications provided by VSD and the underlying iCE40UP5K or iCE40 family FPGA IC.

> ✅ Optimized for use in VSD's open-source FPGA flow and embedded systems integration.

---

## 🧩 Component Overview

| Attribute            | Description                                              |
|---------------------|----------------------------------------------------------|
| **Component Name**  | `VSD_FSM_FPGA`                                           |
| **Target FPGA**     | iCE40UP5K (via VSD Squadron FSM)                         |
| **Pads/Footprint**  | Dual-row header pads with 2.54mm pitch                   |
| **Pin Count**       | 42 (based on board I/O breakout availability)           |
| **Symbol Type**     | Hierarchical/Flat symbol with named I/O groupings        |
| **Pin Mapping**     | Verified against VSD FSM FPGA board documentation        |

---

## 🖼️ Visual Previews

### 1. 📐 Schematic Symbol
![Symbol View]IMAGES\Screenshot 2025-04-16 120811.png)
> Clean hierarchical layout with labeled I/O banks and global nets.

### 2. 📏 PCB Footprint
![Footprint View](PCB and FOOTPRINT/FOOTPRINT/Screenshot 2025-04-16 145350.png)
> Accurate footprint with verified pad locations and silkscreen pin labels.

### 3. 🧱 3D Model (Optional)
![3D Model View](images/3d_model.png)
> Optional STEP model can be added for enclosure fitting and mechanical CAD alignment.

---

## 📐 Footprint Specifications

| Feature                    | Value                              |
|----------------------------|------------------------------------|
| **Pad Pitch**              | 2.54 mm                            |
| **Pin Diameter (hole)**    | 0.9 mm                             |
| **Silkscreen**             | Top layer pin 1 marker, labels     |
| **Mounting Holes**         | Optional – depends on your PCB     |
| **Grid Alignment**         | 100 mil compatible                 |
| **Solder Mask Expansion**  | Default (based on Altium rules)    |

> 📌 Fully compliant with IPC-7351B for through-hole design.

---

## 🧠 Symbol Design Details

- Power pins (`VCC`, `GND`) grouped at top/bottom for readability
- GPIOs organized by **Bank** or **Functionality** (e.g., `UART_TX`, `PWM1`, `I2C_SCL`)
- Global nets like `CLK`, `RST`, and `DONE` are clearly placed
- Each pin includes:
  - Name
  - Direction (Input, Output, Bidirectional)
  - Functional Role (commented or grouped visually)

---

## 🔌 Example I/O Mapping (Partial)

| FPGA Pin | VSD FSM Label | Function      | Direction  |
|----------|----------------|---------------|------------|
| `IOB_16A`| `GPIO_0`       | General I/O   | In/Out     |
| `IOB_20A`| `UART_TX`      | Serial TX     | Output     |
| `IOB_21B`| `UART_RX`      | Serial RX     | Input      |
| `VCCIO1` | `3V3`          | I/O Voltage   | Power      |
| `GND`    | `GND`          | Ground        | Power      |

> Pinout strictly follows VSD documentation. All pins are assigned using the physical pin map and verified via constraint/XDC/PCF mapping.

---

## 🛠️ Integration Workflow

### ✅ Importing into Altium Designer

1. **Download the files** (`.SchLib`, `.PcbLib`, and `images/`)
2. Open your **Altium project**
3. Navigate to:
   - `File > Open > Library`
   - Add `VSD_FSM.SchLib` and `VSD_FSM.PcbLib`
4. Drag and drop into your schematic and layout
5. Use with confidence — **all pads are matched** to the real-world board.

---

## ⚡ Tips for Using the Footprint

- Double-check the board's orientation when placing the footprint (Pin 1 is marked)
- Recommended hole size is 0.9 mm with 1.6 mm annular ring
- Place decoupling capacitors close to the power pins (`VCC`, `GND`)
- For UART, SPI, or JTAG communication, group pins near edge connectors for easy debugging

---

## 🤖 Maze Solver Robot Project Using VSD Squadron FSM FPGA

I have designed and implemented a **Maze Solver Robot** powered by the **VSD Squadron FSM FPGA**. This robot demonstrates real-time decision-making, obstacle detection, and directional control through programmable logic on the FPGA.

### 🚀 Features:

- 🧠 **Powered by VSD FSM FPGA** (based on iCE40UP5K)
- 🌐 **Bluetooth module** for wireless communication and manual override
- ⚙️ **TB6612FNG motor driver** for controlling two **DC encoder motors**
- 🧭 **Three ultrasonic sensors (HC-SR04)** for obstacle detection and wall following
- 🔄 **Maze solving algorithm** executed in finite-state machine logic on FPGA
- 📐 **Single-layer PCB** design with integrated Altium schematic and layout

### 📸 Visual Previews

- **Schematic**: Clean component arrangement with FPGA at the core
- **PCB Layout**: Compact single-layer design for minimal footprint
- **3D Render**: Assembled robot board with component heights and pin alignment

> 📎 All files are part of the project structure and follow standard Altium conventions.

---

## 🧾 File Structure

| Path                     | Description                                      |
|--------------------------|--------------------------------------------------|
| `VSD_FSM.SchLib`         | Schematic symbol for Altium                      |
| `VSD_FSM.PcbLib`         | PCB footprint with silkscreen + drill holes      |
| `MazeSolverProject/`     | Schematic, layout, and images for robot project  |
| `images/`                | Preview images for symbol, footprint, and 3D     |
| `README.md`              | This documentation                              |

---

## 🔗 Resources

- 🔧 [VSD FSM FPGA Docs](https://vsdsquadron.com)
- 📄 iCE40UP5K Datasheet (Lattice)
- 📘 IPC-7351B Footprint Standard
- 📦 [TB6612FNG Motor Driver Datasheet](https://www.sparkfun.com/products/14451)
- 📘 [HC-SR04 Ultrasonic Sensor Guide](https://randomnerdtutorials.com)
- 🧰 [Altium Designer User Guide](https://www.altium.com/documentation)

---

## 👨‍💻 Author

Gowtham.T 
Electronics Design Engineer | FPGA Enthusiast  
[LinkedIn or GitHub profile link]

---

## 📜 License

This symbol/footprint library and maze solver robot files are released under the MIT License.  
Feel free to use, modify, and distribute — with attribution.

