# 🔐 RSA Encryption System on ZYNQ FPGA

<div align="center">

![Platform](https://img.shields.io/badge/Platform-Xilinx%20ZYNQ-orange?style=for-the-badge&logo=xilinx)
![Language](https://img.shields.io/badge/Language-Verilog%20%7C%20C-blue?style=for-the-badge&logo=c)
![Crypto](https://img.shields.io/badge/Algorithm-RSA-red?style=for-the-badge&logo=lock)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=for-the-badge)

**A hardware-software co-design implementation of RSA encryption and decryption on the Xilinx ZYNQ platform**

*Verilog and FPGA Workshop — Project 3*

**Student:** ABABAKER NAZAR ABDELHAMID OSMAN &nbsp;|&nbsp; **Matric No:** A23KE0001

</div>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [System Architecture](#-system-architecture)
- [RSA Parameters](#-rsa-parameters)
- [State Machine](#-state-machine)
- [Hardware Interface](#-hardware-interface)
- [Code Structure](#-code-structure)
- [Results & Demo](#-results--demo)
- [Build Instructions](#-build-instructions)
- [Project Files](#-project-files)

---

## 🌟 Overview

This project implements a **real-time RSA cryptographic system** on the Xilinx ZYNQ SoC, combining the power of FPGA logic with embedded ARM processing. Users can encrypt and decrypt integer messages using physical switches and a push button — no keyboard required.

### ✨ Key Features

| Feature | Detail |
|--------|--------|
| 🔁 **Dual-mode operation** | Toggle between Encrypt and Decrypt via push button |
| 🎛️ **Hardware input** | Message provided via onboard switches |
| 💡 **LED output** | Encrypted/decrypted result shown on LEDs |
| ⚡ **FPGA + ARM** | FPGA handles I/O state machine; ZYNQ ARM runs RSA math |
| 🔢 **RSA algorithm** | Modular exponentiation (square-and-multiply) |

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        ZYNQ SoC                             │
│                                                             │
│  ┌──────────────┐    AXI Bus    ┌────────────────────────┐  │
│  │              │◄─────────────►│                        │  │
│  │  FPGA (PL)   │               │   ARM Cortex-A9 (PS)   │  │
│  │              │               │                        │  │
│  │  • GPIO ctrl │               │  • mod_exp() function  │  │
│  │  • State FSM │               │  • RSA calculation     │  │
│  │  • LED driver│               │  • XGpio interface     │  │
│  └──────┬───────┘               └───────────┬────────────┘  │
│         │                                   │               │
└─────────┼───────────────────────────────────┼───────────────┘
          │                                   │
    ┌─────▼─────┐                       ┌─────▼──────┐
    │ Switches  │                       │    LEDs    │
    │ (Input)   │                       │  (Output)  │
    └───────────┘                       └────────────┘
          │
    ┌─────▼──────┐
    │ Push Button│
    │ (Mode Ctrl)│
    └────────────┘
```

---

## 🔢 RSA Parameters

This project uses a **simplified RSA scheme** for demonstration purposes:

```
┌──────────────────────────────────────────┐
│           RSA Configuration              │
├──────────────────────┬───────────────────┤
│  Modulus (N)         │        15         │
│  Encryption Exp (e)  │         7         │
│  Decryption Exp (d)  │         3         │
│  Key relationship    │  e·d ≡ 1 (mod φN) │
└──────────────────────┴───────────────────┘
```

> **Modular Exponentiation:** `result = base^exponent mod N`
> 
> Uses the efficient **square-and-multiply** algorithm with O(log n) complexity.

### Algorithm (C)

```c
int mod_exp(int base, int exponent, int mod) {
    int result = 1;
    base = base % mod;
    while (exponent > 0) {
        if (exponent % 2 == 1)
            result = (result * base) % mod;
        exponent = exponent >> 1;
        base = (base * base) % mod;
    }
    return result;
}
```

---

## 🔄 State Machine

The push button cycles the system through **3 states**:

```
         ┌─────────────────────────────────┐
         │                                 │
         ▼           BTN press             │
    ┌─────────┐ ─────────────────► ┌───────────────┐
    │ STATE 0 │                    │    STATE 1    │
    │   IDLE  │                    │    ENCRYPT    │
    │ (No op) │ ◄──────────────── │  cipher = m^7 │
    └─────────┘    BTN press x2    └───────┬───────┘
         ▲                                 │
         │                        BTN press│
         │                                 ▼
         │                        ┌───────────────┐
         └──────────────────────  │    STATE 2    │
              BTN press           │    DECRYPT    │
                                  │  plain = c^3  │
                                  └───────────────┘
```

| State | Value | Action | LED Display |
|-------|-------|--------|-------------|
| **IDLE** | `0` | Wait for input | — |
| **ENCRYPT** | `1` | `m^7 mod 15` | Ciphertext |
| **DECRYPT** | `2` | `c^3 mod 15` | Plaintext |

---

## 🎛️ Hardware Interface

### Inputs

| Component | Role |
|-----------|------|
| **Switches [3:0]** | Input message (integer value) |
| **Push Button** | Toggle operation mode |

### Outputs

| Component | Role |
|-----------|------|
| **LEDs [3:0]** | Display encrypted or decrypted result |

### GPIO Configuration (Vitis/ZYNQ)

```c
XGpio_SetDataDirection(&gpio, BTN_CHANNEL, 0xFF);  // Buttons → INPUT
XGpio_SetDataDirection(&gpio, LED_CHANNEL, 0x00);  // LEDs    → OUTPUT
```

---

## 🗂️ Code Structure

```
project3-rsa-zynq/
│
├── 📄 rsa_c_code.c          # Pure C RSA — standalone test (no hardware)
├── 📄 rsa_zynq.c            # ZYNQ embedded C — runs on ARM core
├── 📄 top.v                 # Top-level Verilog (FPGA fabric)
├── 📄 cons.xdc              # ZYNQ constraint file (pin mapping)
│
├── 📁 block_design/
│   └── design_1.bd          # Vivado block design (AXI + GPIO + ZYNQ PS)
│
└── 📄 README.md             # This file
```

---

## ✅ Results & Demo

### Test Case 1 — Encrypt message `9`

```
Input:    9
Operation: 9^7 mod 15
Result:   ✅ Encrypted = 9
```

### Test Case 2 — Decrypt message `2`

```
Input:    2
Operation: 2^3 mod 15
Result:   ✅ Decrypted = 8
```

### Test Case 3 — Encrypt message `3`

```
Input:    3
Operation: 3^7 mod 15
Result:   ✅ Encrypted = 12
```

> All results verified using both the standalone C program and the online [Omni RSA Calculator](https://www.omnicalculator.com/math/rsa).

---

## 🔨 Build Instructions

### Prerequisites

- Xilinx **Vivado 2024.1** (or compatible)
- Xilinx **Vitis 2024.1**
- ZYNQ-7000 development board (e.g., Zybo Z7, ZedBoard)

### Steps

**1. Create the Block Design in Vivado**
```
Open Vivado → New Project → Add design_1.bd
Run Block Automation → Validate Design → Generate Bitstream
Export Hardware (.xsa) including bitstream
```

**2. Build the Software in Vitis**
```
Open Vitis → Create Platform Project (import .xsa)
Create Application Project → Add rsa_zynq.c
Build Project → Launch on Hardware
```

**3. Program the FPGA**
```
Program Device with generated bitstream
Run the ARM application via JTAG/UART
Use switches to input message → push button to encrypt/decrypt → read LEDs
```

---

## 📁 Project Files

🔗 **Full project available here:**

> [Google Drive — Project 3 Files](https://drive.google.com/drive/folders/1WISE3SZAgxaMWay2oY_0_SWD09AhqkLN?usp=sharing)

---

## 📚 Concepts Demonstrated

- ✅ FPGA finite state machine design
- ✅ Hardware-software co-design on ZYNQ SoC
- ✅ AXI GPIO IP integration in Vivado block design
- ✅ RSA modular exponentiation in embedded C
- ✅ Real-time I/O using physical switches, buttons, and LEDs
- ✅ Vitis embedded software development flow

---

<div align="center">

*Verilog & FPGA Workshop — Project 3 | Universiti Malaysia Perlis*

</div>
