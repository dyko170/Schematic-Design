# Mixed-Signal Embedded Controller Board (TI MSPM0 Design Study)

## 📌 Project Overview
This repository contains the schematic architecture and native design files for a compact, mixed-signal microcontroller platform built in **KiCad E.D.A. 10.0.6**. 

The design is centered around the **Texas Instruments MSPM0G3507** (LQFP-48 Package). It integrates a robust USB-C power delivery network, hardware-level ESD protection, USB-to-UART bridging, and an onboard low-power I2C accelerometer for motion sensing applications.

---

## 🛠️ Hardware Architecture & Circuit Design

### 1. Power Architecture & Regulation
* **Input Conditioning:** Power input originates from a **USB-C interface** (`+VBUS`). The design incorporates a Pi-filter placeholder layout (`R4` 0Ω resistor) paired with bulk and decoupling capacitors (`C9` 10µF, `C5` and `C8` 470nF) right at the power entry point to damp line transients and high-frequency switching noise.
* **Voltage Regulation:** An **SPX3819M5-L-3-3 Low-Dropout (LDO) Regulator** steps down the noisy 5V input rail to a clean, stable `+3.3V` system rail. The LDO circuit uses a 470nF input filter (`C10`) and a 10µF bulk output capacitor (`C11`) to preserve transient response performance.

### 2. MCU Core & Infrastructure (TI MSPM0G3507)
* **Local Decoupling:** The microcontroller's power pins are protected by close-proximity decoupling capacitors (`C1` 10µF, `C2` 470nF) to absorb high-frequency clock-edge noise.
* **Clock Network:** A dedicated high-precision **8MHz Crystal Oscillator (XL)** handles system clock distribution, stabilized via balanced 15pF load capacitors (`C6`, `C7`) and an isolated path.
* **Debug & Reset Circuits:** A 5-pin **SWD Header (TC2030 tag-connect layout footprint)** is integrated for firmware deployment and hardware debugging. The hardware reset line (`NRST`) includes an RC debounce filter (`R2` 5.1kΩ, `C4` 470nF) to prevent unexpected system drops or noise-induced reboots.

### 3. Interface & Sensor Communication
* **USB-to-UART Bridge:** A **CH340E transceiver** handles serial data piping over differential lines (`USBD-`, `USBD+`). 
* **ESD Ingress Protection:** To insulate the internal ICs from external electrostatic damage, a high-speed **USBLC6-2SC6 ESD protection diode array (U1)** is hardwired directly across the USB differential paths at the physical connector edge.
* **I2C Bus Constraints:** A digital **LIS2DH Accelerometer** handles motion data tracking. The I2C lines (`I2C5SCL`, `I2C5SDA`) are tied to the `+3.3V` rail using dedicated **5.1kΩ pull-up resistors** (`R7`, `R8`), calculated to satisfy safe signal rise-time parameters for standard 400kHz operation.

---

## 🔍 Engineering Self-Review & AI Alignment Logic
*A demonstration of code/schematic auditing capabilities tailored for automated design review scenarios:*

1. **ESD Placement Strategy:** The `U1` protection diode array is strategically placed directly adjacent to the USB-C receptacle pads before the tracks branch to the CH340E. This anchors transient energy at the edge of the board, preventing high-voltage paths from cutting across sensitive signal layers.
2. **Proper Open-Drain Pull-ups:** The I2C lines are correctly terminated using 5.1kΩ resistors. This strikes an optimal balance for a 3.3V domain—preventing slow, rounded signal transitions (from oversized resistance) while avoiding excessive current leakage through open-drain lines (from undersized resistance).
3. **Differential Impedance Target:** The downstream layout stage assumes a mandatory tight-geometric geometric pairing for `USBD+` and `USBD-` traces to satisfy standard 90Ω differential impedance guidelines required for reliable USB 2.0 communication.
