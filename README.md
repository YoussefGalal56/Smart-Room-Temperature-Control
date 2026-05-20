
## Project - 🌡️ Smart Room Temperature Control

![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=flat-square)
![Module](https://img.shields.io/badge/Module-Digital%20Electronics%20(4FTC2027)-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Arduino%20Uno-00979D?style=flat-square&logo=arduino&logoColor=white)
![Language](https://img.shields.io/badge/Language-C%20(OOP)-lightgrey?style=flat-square)

> An Arduino-based smart room temperature control system that monitors temperature and humidity using a DHT22 sensor and controls a 5V fan via an L298N H-Bridge motor driver. Supports Automatic and Manual operating modes with full LCD feedback.

**Module Leader:** Dr. Samar Moustafa

</div>

---

## Overview

A two-mode fan control system built on an Arduino Uno:

- **Automatic Mode** — reads temperature from a DHT22 sensor and sets fan speed based on defined thresholds
- **Manual Mode** — user directly controls fan speed (Off / Low / Medium / High) via push buttons

All status information — temperature, humidity, fan speed, and active mode — is displayed live on a 16×2 I2C LCD.

---

## Operating Modes

| Mode | Trigger | Behaviour |
|------|---------|-----------|
| **Automatic (AU)** | Auto/Manual toggle button | Fan speed determined by temperature thresholds |
| **Manual (MA)** | Auto/Manual toggle button | User selects fan speed via Off / Low / Med / High buttons |

**Automatic mode temperature thresholds:**

```
≤ 25°C        →  Fan Off
25°C – 30°C   →  Low
30°C – 35°C   →  Medium
> 35°C        →  High
```

---

## Hardware

| Component | Qty | Purpose |
|-----------|-----|---------|
| Arduino Uno | 1 | Main microcontroller |
| DHT22 sensor | 1 | Temperature and humidity readings |
| 16×2 I2C LCD | 1 | Displays temp, humidity, fan speed, and mode |
| L298N H-Bridge | 1 | Drives the 5V fan with variable PWM speed |
| 5V fan | 1 | Cooling output |
| Push buttons | 5 | Off / Low / Med / High + Auto/Manual toggle |
| 4.7kΩ resistor | 1 | Pull-up resistor on DHT22 data pin |
| Battery cells | 3 | Power the motor driver independently |
| Breadboard + jumper wires | — | Circuit assembly |

**Pin mapping:**

```
D2   →  Auto/Manual toggle button
D3   →  Off button
D4   →  Low button
D5   →  Medium button
D6   →  High button
D8   →  DHT22 data pin
D9   →  L298N ENB (PWM speed control)
D11  →  L298N IN3
D12  →  L298N IN4
A4   →  I2C LCD SDA
A5   →  I2C LCD SCL
```

---

## Software Architecture

Object-oriented programming (OOP) structure — one `.ino` file per component:

| File | Responsibility |
|------|---------------|
| `main.ino` | Setup, 1-second main loop, mode switching logic |
| `Buttons.ino` | `fanSpeedBtns()` — reads speed buttons with input pull-ups; `toggleAuto()` — edge-detected auto/manual toggle |
| `DHTSensor.ino` | `readTemperature()` / `readHumidity()` — returns `-1` on sensor error |
| `LCD.ino` | `setupLCD()` — shows "System Ready" on boot; `updateLCD()` — displays T, H, speed, and mode every loop |
| `MotorDriver.ino` | `autoControl()` — maps temperature to `fanMode`; `manualControl()` — passes button input to `fanMode`; `applyFanMode()` — writes PWM via `analogWrite(ENB, ...)` |
| `hardware.h` | Pin definitions and temperature threshold constants |
| `functions.h` | Forward declarations for all functions |

**PWM speed values:**

```
Off     →  analogWrite(ENB, 0)
Low     →  analogWrite(ENB, 150)
Medium  →  analogWrite(ENB, 190)
High    →  analogWrite(ENB, 255)
```

---

## Testing & Results

| Stage | Method | Outcome |
|-------|--------|---------|
| Code verification | Arduino IDE syntax check | Passed |
| Simulation | Full circuit test in SimulIDE | Worked as expected |
| Hardware prototype | Breadboard assembly and testing | Auto and Manual modes functional; Low speed adjusted |
| Final test | Complete system on physical hardware | All modes working as intended |

---

## Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Low fan speed insufficient to start rotation | Increased Low PWM value; redistributed speed steps so fan receives enough voltage |
| Wrong I2C address on physical LCD (different from SimulIDE) | Updated address in `LCD.ino` to match the physical hardware |
| Battery voltage drop causing Low mode to fail after use | Identified as drain issue; rechargeable batteries used as workaround |

---

## Future Improvements

- Remote control via a mobile application
- User-configurable temperature thresholds set through the interface
- Higher-accuracy sensor to replace the DHT22
- Energy consumption display for efficiency monitoring
- Physical enclosure with buttons accessible from the top panel
