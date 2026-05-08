# 🚻 SmartSan+
**An ESP32-based smart bathroom monitoring system built with MicroPython.**

SmartSan+ tracks stall occupancy, air quality, water levels, restocking needs, and emergency alerts — all in real time with buzzer notifications and serial output.

---

## 📐 Circuit Diagram

![SmartSan+ Wiring Diagram](Circuit_diagram.jpeg)

> **Make sure `Circuit_diagram.jpeg` is in the same folder as this `README.md` in your repository.**

---

## 🧰 Components

| Component | Purpose | GPIO |
|---|---|---|
| VL53L0X ToF sensor | Stall occupancy detection | SDA 21 / SCL 22 |
| LCD (I2C) | Status display | SDA 21 / SCL 22 |
| MQ135 gas sensor | Odor / air quality monitoring | 34 (analog) |
| IR sensor 1 | Entrance outer (direction count) | 26 |
| IR sensor 2 | Entrance inner (direction count) | 25 |
| IR sensor 3 | Basin approach detection | 27 |
| IR sensor 4 | Dispenser stock detection | 33 |
| Water sensor | Cleaning verification | 35 (analog) |
| Active buzzer | Alerts (SOS, cleaning, restock) | 15 |
| Green LED | Vacant indicator | 2 |
| Red LED | Occupied indicator | 4 |
| Yellow LED | Warning indicator | 5 |
| SOS button | Emergency alert | 13 (PULL_UP) |
| Cleaning button | Trigger cleaning mode | 12 (PULL_UP) |
| Maintenance button | Trigger maintenance mode | 14 (PULL_UP) |

---

## ⚙️ How It Works

### Occupancy
The VL53L0X time-of-flight sensor is mounted above the stall. If the measured distance drops below `DISTANCE_THRESHOLD` (800 mm) for **3 consecutive readings**, the stall is marked **OCCUPIED**.

### Direction Counting (Entrance)
Two IR sensors at the entrance (8–12 cm apart) determine entry/exit direction:
- **IR1 → IR2** triggered = person **entered**
- **IR2 → IR1** triggered = person **exited**

### Air Quality
The MQ135 continuously reads analog air quality. If the value exceeds `GAS_THRESHOLD` (2500) for 3 stable readings, a **cleaning alert** is raised.

### Water Level
The water sensor analog value is checked each loop. If it falls below `WATER_THRESHOLD` (1000) for 3 stable readings, a **water refill alert** is raised.

### Dispenser Stock
IR4 is mounted inside the dispenser facing downward:
- Object detected → stock **present**
- No object → stock **low**, restock alert raised

### Buzzer Alerts
The buzzer fires on any of these conditions:
- `cleaning_required`
- `restock_required`
- `water_low`
- `sos_alert`

A 5-second cooldown (`ALERT_COOLDOWN`) prevents buzzer spam.

### SOS
Pressing the SOS button triggers an immediate buzzer alert and serial `EMERGENCY SOS ALERT` message. Debounced with a 50 ms delay check.

---

## 📁 File Structure

```
smartsan-plus/
├── main.py                 # Main loop and all sensor logic
├── vl53l0x.py              # VL53L0X MicroPython driver
├── Circuit_diagram.jpeg    # Wiring diagram image
└── README.md
```

---

## 🚀 Setup

1. Flash **MicroPython** onto your ESP32.
2. Upload `vl53l0x.py` (driver) and `main.py` to the board using **Thonny** or `mpremote`.
3. Wire components as shown in the circuit diagram above.
4. Power the ESP32 via USB and all other components via the 5V 2A supply with a shared GND.
5. Open the serial monitor (115200 baud) to view live output.

---

## 📊 Serial Output Example

```
================================
 SMARTSAN+ SYSTEM STARTED
================================

================================
STATUS: VACANT
Distance: 1200 mm
Gas Value: 1800
Stock Available
Water Value: 1500
Water OK
Usage Count: 3
================================
```

---

## 🔧 Thresholds (Configurable in `main.py`)

| Variable | Default | Description |
|---|---|---|
| `GAS_THRESHOLD` | 2500 | MQ135 value above which cleaning is required |
| `WATER_THRESHOLD` | 1000 | Water sensor value below which refill is needed |
| `DISTANCE_THRESHOLD` | 800 | VL53L0X distance (mm) below which stall is occupied |
| `ALERT_COOLDOWN` | 5 s | Minimum seconds between buzzer alerts |

---

## 📌 Notes

- The LCD and VL53L0X **share the same I2C bus** (GPIO 21/22). Ensure their I2C addresses do not conflict (LCD default `0x27`, VL53L0X default `0x29`).
- All buttons use internal **PULL_UP** — no external resistors needed. Unpressed = `1`, pressed = `0`.
- All LEDs require a **220Ω series resistor**.
- MQ135 and Water sensor use `ADC.ATTN_11DB` for a full 0–3.3V input range.
- GPIO 34 and 35 are **input-only** pins on ESP32 — ideal for analog sensors.

---

## 📄 License

MIT License — free to use and modify.
