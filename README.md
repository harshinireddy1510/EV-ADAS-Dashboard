# 🚗 EV-ADAS-Dashboard

Real-time Electric Vehicle ADAS Dashboard built on STM32F103C8T6 with ultrasonic sensing, UART telemetry, and a Python-based monitoring dashboard for vehicle diagnostics and safety visualization.

**STM32F103C8T6 Blue Pill | Embedded C | STM32 HAL | HC-SR04 | UART | Python Dashboard | PICSimLab**

![STM32](https://img.shields.io/badge/STM32-Blue%20Pill-blue)
![Embedded C](https://img.shields.io/badge/Language-Embedded%20C-success)
![Python](https://img.shields.io/badge/Python-3.x-yellow)
![UART](https://img.shields.io/badge/Communication-UART-orange)
![Automotive](https://img.shields.io/badge/Domain-Automotive-red)
![Status](https://img.shields.io/badge/Project-Completed-brightgreen)

</div>

---

# 📖 Project Overview

**EV-ADAS-Dashboard** is a real-time automotive embedded systems project that simulates the behaviour of an Electric Vehicle (EV) integrated with Advanced Driver Assistance System (ADAS) functionalities.

The project is built around the **STM32F103C8T6 Blue Pill** microcontroller using the **STM32 HAL Framework**. It continuously acquires vehicle inputs, processes EV dynamics, evaluates ADAS safety conditions, detects faults, and streams telemetry to a Python dashboard through UART.

The firmware follows a modular Electronic Control Unit (ECU) architecture, separating EV control, ADAS logic, sensor drivers, fault management, and communication into independent software modules.

The complete system is designed to execute deterministic real-time control loops while maintaining safe vehicle operation through continuous monitoring and fault handling.

---

# 🎯 Objectives

- Develop a modular embedded software architecture for an EV controller.
- Implement real-time EV parameter estimation.
- Integrate ADAS safety features using ultrasonic sensors.
- Detect critical faults and safely transition to FAULT state.
- Stream live telemetry to a Python dashboard using UART.
- Simulate the complete embedded system using PICSimLab.

---

# ✨ Key Features

## 🚘 Electric Vehicle Monitoring

- Real-Time Vehicle Speed Estimation
- Battery State of Charge (SOC)
- Remaining Driving Range Estimation
- Motor Torque Calculation
- Motor Power Monitoring
- Motor Temperature Monitoring
- Regenerative Braking
- Multiple Drive Modes

---

## 🛡️ Advanced Driver Assistance System

- Forward Collision Warning (FCW)
- Blind Spot Detection
- Parking Assistance
- Time-To-Collision (TTC)
- Overspeed Detection
- Priority-Based Alarm Generation

---

## ⚙ Embedded Features

- STM32 HAL Driver Framework
- Modular Firmware Architecture
- 12-bit ADC Data Acquisition
- UART DMA Communication
- Timer Based Scheduling
- Interrupt Driven Design
- Finite State Machine
- Fault Detection and Recovery
- Watchdog Friendly Scheduler

---

# 🏗 System Architecture

```
                +------------------------------+
                |      Python Dashboard        |
                | matplotlib + pyserial        |
                +--------------▲---------------+
                               │ UART (115200)
                               │
+-------------------------------------------------------------+
|                 STM32F103C8T6 Blue Pill                      |
|                                                             |
|  EV Controller                                              |
|  ADAS Engine                                                |
|  Fault Manager                                              |
|  UART Shell                                                 |
|  Scheduler                                                  |
+-----------▲----------------------▲--------------------------+
            │                      │
      ADC Inputs              HC-SR04 Sensors
 (Speed, Brake, SOC, Temp)   Front • Left • Right
```

---

# 🚘 EV Control System

The EV Controller executes a **100 Hz** control loop responsible for calculating all primary vehicle parameters.

### Responsibilities

- Accelerator ADC Sampling
- Brake ADC Sampling
- Vehicle Speed Estimation
- Motor Torque Calculation
- Power Calculation
- Battery SOC Estimation
- Remaining Range Prediction
- Regenerative Braking
- Drive Mode Management

---

# ⚡ Drive Modes

Three runtime-selectable drive modes are implemented.

| Mode | Torque Scaling | Description |
|------|---------------|-------------|
| ECO | **0.6×** | Maximum driving efficiency |
| NORMAL | **1.0×** | Balanced performance |
| SPORT | **1.3×** | Maximum acceleration |

The selected mode directly influences torque output, power consumption and estimated driving range.

---

# 🔋 Battery Management

Battery behaviour is estimated continuously during operation.

### Features

- State of Charge Estimation
- Remaining Driving Range
- Power Consumption Monitoring
- Regenerative Charging

### Battery Thresholds

| SOC | Status |
|------|--------|
| >30% | Normal |
| 10–30% | Warning |
| <10% | Critical |
| <2% | FAULT State |

When SOC falls below **2%**, the Fault Manager immediately disables propulsion and transitions the vehicle into the FAULT state.

---

# 🌡 Motor Temperature Monitoring

Motor temperature is monitored continuously.

| Temperature | System Response |
|-------------|----------------|
| ≤70°C | Normal |
| >70°C | Advisory |
| >90°C | FAULT State |

If motor temperature exceeds **90°C**, PWM output is disabled and a critical fault is generated.

---

# 🛡 Advanced Driver Assistance System

The ADAS Engine executes every **100 ms** and processes data from three HC-SR04 ultrasonic sensors.

The sensors provide:

- Front Obstacle Detection
- Left Blind Spot Detection
- Right Blind Spot Detection
- Parking Assistance
- Time-To-Collision Estimation

---

# 🚨 Forward Collision Warning

The front ultrasonic sensor continuously evaluates obstacle distance and Time-To-Collision.

### Collision Thresholds

| Condition | Alarm |
|------------|-------|
| Distance <50 cm OR TTC <3 s | WARNING |
| Distance <20 cm OR TTC <1.5 s | CRITICAL |

Critical collision immediately:

- Stops motor PWM
- Activates Red LED
- Displays FAULT banner
- Transitions vehicle into FAULT state

---

# 🚘 Blind Spot Detection

Side sensors continuously monitor adjacent lanes.

### Detection Logic

Vehicle Speed >20 km/h

AND

Side Distance <30 cm

↓

Blind Spot Detected

### Response

- Yellow LED ON
- Dashboard Warning
- Advisory Alarm

---

# 🅿 Parking Assistance

Parking assistance continuously measures surrounding obstacle distances.

Obstacle information is visualized on the dashboard using a bird-eye representation, allowing safe low-speed manoeuvring.

---

# ⏱ Time-To-Collision (TTC)

Collision probability is estimated using

```
TTC = Front Distance / Vehicle Speed
```

### TTC Thresholds

| TTC | Status |
|------|--------|
| >3 s | Safe |
| <3 s | Warning |
| <1.5 s | Critical |

---

# ⚠ Fault Management

The Fault Manager continuously monitors all critical vehicle conditions.

Automatic fault triggers include

- Motor Temperature >90°C
- Battery SOC <2%
- Critical Collision Detection

Upon fault detection:

- Vehicle enters FAULT State
- PWM Disabled
- Alarm Activated
- Dashboard Updated
- Telemetry Transmitted

---

# 🚦 Alarm Priority Levels

| Priority | Description |
|----------|-------------|
| **P0** | Normal |
| **P3** | Advisory |
| **P2** | Warning |
| **P1** | Critical |

### Alarm Thresholds

| Parameter | Warning | Critical |
|------------|---------|-----------|
| Front Distance | <50 cm | <20 cm |
| TTC | <3 s | <1.5 s |
| Motor Temperature | >70°C | >90°C |
| Battery SOC | <10% | <2% |
| Blind Spot | <30 cm | — |
| Overspeed | >120 km/h | — |

---

# 🚦 Vehicle State Machine

```
PARKED
   │
Accel >2%
   ▼
READY
   │
Accel >5%
   ▼
DRIVING
   │
Brake >10%
   ▼
REGENERATIVE BRAKING
   │
Brake <5%
   ▼
DRIVING

Any Critical Fault
        │
        ▼
      FAULT
```

---

# 📡 UART Communication

Communication is performed through **USART1**.

| Parameter | Value |
|-----------|-------|
| Baud Rate | 115200 bps |
| Packet Rate | 10 Hz |
| Transmission | DMA |
| Reception | Interrupt Ring Buffer |

---

# 📦 Telemetry Packets

## Packet 0x01 — EV Metrics

- Vehicle Speed
- Battery SOC
- Motor Torque
- Motor Power
- Remaining Range
- Motor Temperature
- Drive Mode

---

## Packet 0x02 — ADAS Alerts

- Front Distance
- Left Distance
- Right Distance
- Collision Level
- Blind Spot Status
- Time-To-Collision

---

# 📊 Python Dashboard

The Python Dashboard provides a Tesla-inspired digital instrument cluster updated at **10 Hz**.

### Dashboard Widgets

- Digital Speedometer
- Battery Gauge
- Remaining Range
- Motor Torque
- Power Meter
- Motor Temperature
- Drive Mode Indicator
- Bird-Eye ADAS View
- Blind Spot Indicators
- Collision Warning Banner
- Fault Status

---

# 📁 Project Structure

```text
EV-ADAS-Dashboard/
│
├── Core/
│   ├── Inc/
│   └── Src/
│
├── Drivers/
│
├── Startup/
│
├── Python/
│
├── Docs/
│
├── Images/
│
├── README.md
├── LICENSE
├── .gitignore
├── EV_ADAS.ioc
├── .project
└── .cproject
```

---

# 🛠 Hardware

- STM32F103C8T6 Blue Pill
- HC-SR04 ×3
- Potentiometers ×4
- LEDs
- UART Interface

---

# 💻 Software

- STM32CubeIDE
- STM32 HAL
- Embedded C
- Python
- PICSimLab

---

# 🚀 Technologies

- Embedded C
- STM32 HAL
- UART
- DMA
- ADC
- GPIO
- Timers
- Interrupts
- HC-SR04
- Python
- Matplotlib
- PySerial
- Git
- GitHub

---

# 🎯 Future Enhancements

- CAN Bus Integration
- FreeRTOS Scheduler
- GPS Navigation
- Cloud Connectivity
- Mobile Application
- Adaptive Cruise Control
- Lane Keeping Assist
- AI-Based Driver Monitoring
- OTA Firmware Updates

---

# 👨‍💻 Author

**Harshini**

**Electronics and Communication Engineering**

**Embedded Systems • Automotive Software • Electric Vehicles • STM32 • ADAS**

---

<div align="center">

⭐ **If you found this project interesting, consider giving it a Star!**

</div>
