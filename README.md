<div align="center">

# 🚽 SmartSan+ 
### *Industrial-Grade Intelligent Sanitation Infrastructure*

[![Platform: ESP32](https://img.shields.io/badge/Hardware-ESP32-blue?style=for-the-badge&logo=espressif)](https://www.espressif.com/)
[![Logic: Stable IoT](https://img.shields.io/badge/Logic-Stabilized_Architecture-orange?style=for-the-badge)](https://github.com/)
[![Language: MicroPython](https://img.shields.io/badge/Language-MicroPython-yellow?style=for-the-badge&logo=python)](https://micropython.org/)

<p align="center">
  <img src="https://img.icons8.com/fluency/96/000000/smart-home.png" width="100" alt="Smart Home">
</p>

[Technical Architecture](#-technical-architecture) • [Bill of Materials](#-bill-of-materials) • [Installation](#-installation)

</div>

---

## 🚀 The Innovation: "Stabilized IoT"
Most smart restrooms fail in real-world environments due to **Sensor Noise** and **Alert Spam**. **SmartSan+** implements industrial software stabilization techniques (Moving Averages and State Confirmation) to ensure every alert is verified and actionable.

### **Key Performance Features**
* **Anti-Flicker Occupancy:** Uses a 3-cycle confirmation buffer for the VL53L0X Laser sensor.
* **Odor Moving Average:** MQ-135 readings are filtered through a 5-sample window to ignore momentary spikes.
* **One-Shot Alerts:** Active buzzer logic includes a 5-second "Alert Cooldown" to prevent repetitive noise.
* **Resource Accountability:** IR-based stock monitoring with 3s delay confirmation.

---

## 🏗 Technical Architecture

### **Logic Flow & Hardware Schematic**
The system is built on a **Dual-Node Sentinel Architecture** optimized for zero-latency emergency response and high-accuracy monitoring.

<p align="center">
  <img src="https://raw.githubusercontent.com/username/repo/main/assets/Gemini_Generated_Image_d48r2pd48r2pd48r.png" alt="SmartSan+ Simplified Circuit Diagram" width="900">
  <br>
  <b>Figure 1:</b> <i>Stabilized Hardware Schematic (Simplified for Hackathon Demo)</i>
</p>

---

## 🧠 System Logic (State Machine)

| Feature | Sensor | Stabilization Strategy |
| :--- | :--- | :--- |
| **Occupancy** | VL53L0X | **Confidence Counter:** Must maintain <800mm for 3 cycles to trigger "Occupied." |
| **Air Quality** | MQ-135 | **Moving Average:** Filters noise from the heater element for a stable PPM baseline. |
| **Restock** | IR Sensor | **Hysteresis Logic:** Alerts only after 3 continuous seconds of empty-state detection. |
| **SOS Alert** | Push Button | **Hardware Interrupt:** Immediate response with software-level debounce protection. |

---

## 📦 Bill of Materials

Final procurement list optimized for high sensitivity and cost-effectiveness:

| Component | Quantity | Purpose | Source |
| :--- | :---: | :--- | :--- |
| **VL53L0X Laser Sensor** | 1 | Millimeter-accurate Stall Occupancy | Robocraze |
| **MQ-135 Gas Module** | 1 | Ammonia ($NH_3$) & Odor Analytics | Robocraze |
| **Water Level Sensor** | 1 | Cleaning/Hydraulic Verification | Robocraze |
| **Active Buzzer (3.3V-5V)** | 1 | Emergency SOS Audio Output | Robocraze |
| **Tactile Buttons (10pk)** | 1 | SOS & Maintenance Reset | Robocraze |
| **ESP32-WROOM-32** | 1 | Core Logic & WiFi Dashboard | - |

---

## 📱 IoT Command Center (Blynk 2.0)

Facility managers receive a real-time "God-Eye View" of the restroom infrastructure.

<p align="center">
  <img src="https://img.icons8.com/color/480/000000/dashboard.png" width="300" alt="Dashboard Placeholder">
  <br>
  <i>(Replace with your Blynk Dashboard Screenshot)</i>
</p>

---

## 💻 Installation

### 1. Hardware Calibration
> [!WARNING]
> The **MQ-135** requires a 10-15 minute pre-heat period before readings become stable. Do not calibrate thresholds during the first 5 minutes of power-on.

### 2. Software Deployment
1. Install the `micropython-vl53l0x` library via Thonny.
2. Configure your `BLYNK_AUTH_TOKEN` in the code.
3. Upload `main.py` to the ESP32.

```python
# To test library installation
from vl53l0x import VL53L0X
print("SmartSan+ Stabilized Driver: Ready")
