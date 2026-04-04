# init-flight-stack

![Status](https://img.shields.io/badge/status-in%20development-yellow)
![License](https://img.shields.io/badge/license-MIT-blue)
![Platform](https://img.shields.io/badge/platform-Raspberry%20Pi%20Pico-red)
![Language](https://img.shields.io/badge/language-C-lightgrey)

A mini brushed DC quadcopter built entirely from scratch. No off-the-shelf flight controller software. Custom hardware, custom firmware, custom drivers.

---

## Table of Contents

- [Overview](#overview)
- [Hardware](#hardware)
  - [Drone](#drone)
  - [RC Controller](#rc-controller)
- [Firmware](#firmware)
- [Project Structure](#project-structure)
- [Build Status](#build-status)
- [License](#license)

---

## Overview

This project is a complete from-scratch implementation of a mini quadcopter and its RC controller. The goal is to understand and build every layer of the stack — from raw sensor data to stable flight — without relying on existing flight controller frameworks.

**Key design decisions:**
- Bare metal C using the Raspberry Pi Pico C/C++ SDK
- Dual-core RP2040: Core 0 handles flight control, Core 1 handles radio
- Custom drivers written from scratch for nRF24L01 and MPU6050
- No FreeRTOS, no MicroPython, no Betaflight

---

## Hardware

### Drone

| Component | Part | Notes |
|---|---|---|
| Flight controller | Raspberry Pi Pico | RP2040 dual-core |
| IMU | MPU6050 | I2C1, GP14/GP15, addr 0x68 |
| Radio | nRF24L01 SMD | SPI0, GP6-GP10, PCB antenna |
| Motor driver | TA6586 x4 | PWM on IN1 (GP0-GP3), IN2 to GND |
| Motors | 8520 coreless brushed DC | 3.7V, ~50g thrust each, 1mm shaft |
| Battery | 1S LiPo 360mAh 30C | JST-PH 1.25mm, direct to VSYS |
| Buzzer | Piezo | GP16 |
| Frame | Custom 3D printed | 75-100mm diagonal |

**Power architecture:**
- 1S LiPo (3.7-4.2V) direct to Pico VSYS
- Pico 3.3V rail powers nRF24L01 and MPU6050
- LiPo direct to TA6586 VM pins
- No external voltage regulators

**AUW:** ~67g estimated. Ceiling: 133g (1.5:1 thrust ratio).

### RC Controller

| Component | Part | Notes |
|---|---|---|
| MCU | Raspberry Pi Pico | RP2040 |
| Radio | nRF24L01 PA+LNA | SPI0, GP6-GP10, external antenna |
| Analog mux | CD4051 | 4 joystick axes via GP26 (ADC0) |
| Joysticks | 2x analog with button | Axes to CD4051, buttons to GP2-GP3 |
| Switches | 2x latching | GP4 arm/disarm, GP5 flight mode |
| Display | 0.96 inch OLED | I2C1, GP14-GP15 |
| Buzzer | Piezo | Pair and signal loss indicator |
| Battery | 18650 cell | Direct to VSYS, TP4056 charging |

**Controls mapping:**

| Input | Function |
|---|---|
| Left stick Y | Throttle |
| Left stick X | Yaw |
| Right stick Y | Pitch |
| Right stick X | Roll |
| Switch 1 | Arm / Disarm |
| Switch 2 | Flight mode (reserved) |
| Left stick button | Pairing |
| Right stick button | OLED navigation |

---

## Firmware

Written in bare metal C using the Pico C/C++ SDK. Structured across three repositories:

| Repository | Description |
|---|---|
| [pico-nrf24l01](#) | Custom nRF24L01 driver for Pico C SDK |
| [pico-mpu6050](#) | Custom MPU6050 driver for Pico C SDK |
| init-flight-stack | Main drone and controller firmware (this repo) |

**Radio protocol:**
- 12-byte packets at 100Hz default (configurable)
- 6 channels: throttle, yaw, pitch, roll, arm, flight mode
- Failsafe: motors cut immediately after 500ms signal loss
- Drone buzzer activates continuously on signal loss for crash location

**Dependencies:**
- [pico-ssd1306](https://github.com/daschr/pico-ssd1306) — OLED display

---

## Project Structure
```
init-flight-stack/
├── drone/
│   ├── src/
│   └── include/
├── controller/
│   ├── src/
│   └── include/
└── docs/
```

> Structure will be populated starting Phase 2.

---

## Build Status

| Phase | Status |
|---|---|
| Phase 1 - Requirements and Architecture | Complete |
| Phase 2 - Hardware Design | In progress |
| Phase 3 - Firmware Development | Pending |
| Phase 4 - Mechanical Design | Pending |
| Phase 5 - Integration and Bench Testing | Pending |
| Phase 6 - Flight Testing and Tuning | Pending |

---

## License

MIT License. See [LICENSE](LICENSE) for details.
