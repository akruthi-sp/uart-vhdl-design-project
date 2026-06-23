## Overview

This project presents the design and implementation of a **Universal Asynchronous Receiver and Transmitter (UART)** protocol using VHDL. UART is a widely used serial communication interface that converts parallel data to serial data for transmission and vice versa for reception. This implementation supports configurable baud rates, parity-based error detection, and frame error detection.

## Features

- Full-duplex UART communication (simultaneous transmit and receive)
- Configurable baud rate generator (8 selectable baud rates)
- Parity error detection (even/odd parity)
- Frame error detection
- 8-bit data word with start, stop, and parity bits
- LSB-first serial transmission

## Architecture

The UART design consists of three main modules:

### 1. Transmitter
- Accepts 8-bit parallel data as input
- Appends start bit, parity bit, and stop bit
- Converts parallel data to serial and outputs via `TxD` pin
- Controlled by load and reset signals

### 2. Receiver
- Detects incoming serial data on `RX` pin
- Strips start, stop, and parity bits
- Converts serial data back to 8-bit parallel output via `data_out`
- Detects and flags **Frame Error (FE)** and **Parity Error (PE)**

### 3. Baud Rate Generator
- Divides an 8 MHz base clock to generate standard baud rates
- 3-bit select input chooses from 8 available baud rates
- Generates `Bclk` and `BclkX8` timing signals for TX/RX synchronization

## Baud Rate Options (8 MHz Clock)

| Select Bits | Baud Rate |
|---|---|
| 000 | 1200 |
| 001 | 2400 |
| 010 | 9600 (≈ 9615) |
| 011 | 19200 |
| ... | ... |

## UART Frame Format

```
[ START | D0 | D1 | D2 | D3 | D4 | D5 | D6 | D7 | PARITY | STOP ]
```

- **Start bit:** Logic LOW (active low signals new transmission)
- **Data bits:** 8 bits, transmitted LSB first
- **Parity bit:** Even or odd (XOR of all data bits)
- **Stop bit:** Logic HIGH

## Error Detection

| Error Type | Description |
|---|---|
| **Frame Error (FE)** | Receiver expects a start bit but does not find it (synchronization failure) |
| **Parity Error (PE)** | Parity computed at receiver does not match the transmitted parity bit |

## Project Structure

```
uart-vhdl-design/
├── rtl/
│   ├── uart_top.vhd          # Top-level UART module
│   ├── transmitter.vhd       # Transmitter module
│   ├── receiver.vhd          # Receiver module
│   └── baud_rate_gen.vhd     # Baud rate generator
├── tb/
│   └── uart_tb.vhd           # Testbench for full UART simulation
├── sim/
│   └── run_sim.do            # ModelSim simulation script
└── docs/
    └── project_report.pdf    # Full project report
```

## Simulation Results

### Transmitter
- Start bit transmitted at **180 µs**
- 8 data bits follow sequentially
- Parity bit transmitted at **330 µs**
- Stop bit transmitted at **350 µs**

### Receiver
- Stop bit detected at **39 µs**
- Start bit of next frame detected at **43 µs**
- Parallel data output available at `data_out` pin

### Baud Rate Generator
- With select input `"010"`, generated baud rate = **9615 baud** (≈ 9600 bps)

### Full UART Module
- Start bit detected at **580 ms**
- Parity bit detected at **1060 ms**
- Stop bit detected at **1120 ms**

## Tools Used

- **VHDL** — Hardware description and implementation
- **ModelSim** — Functional simulation and waveform analysis

## Pin Description

| Pin | Direction | Description |
|---|---|---|
| MCLK | Input | Master clock |
| MRST | Input | Master reset |
| LOAD | Input | Load data into transmitter |
| DATA[7:0] | Bidirectional | 8-bit parallel data |
| TxD | Output | Serial transmit output |
| RX | Input | Serial receive input |
| PARITYERR | Output | Parity error flag |
| FRAMINGERR | Output | Frame error flag |

## Author

**Akruthi S P**  
UART Protocol Design Project

---
*Implemented and simulated using VHDL and ModelSim*
