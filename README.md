# ESP32 RTOS Overvoltage Voltmeter with Web Dashboard

## Quick Use

1. Upload the sketch to the ESP32
2. Power the device
3. Connect to WiFi

   * SSID: `ESP32-Voltmeter`
   * Password: `12345678`
4. Open a browser and go to:

   ```
   http://192.168.4.1
   ```
5. Monitor voltage, relay state, and overvoltage status
6. Press **RESET LATCH** on the web page to recover after overvoltage

---

## Overview

This project is an **ESP32-based voltage monitoring and protection system** built using **FreeRTOS (Arduino ESP32 core)**.
It continuously measures an analog voltage, disconnects a relay when an overvoltage is detected, plays an audible alarm, and provides real-time monitoring via a web dashboard.

---

## Main Features

* Real-time voltage monitoring
* Overvoltage latch protection
* Relay cutoff for safety
* Audible alarm (buzzer melody)
* Web-based dashboard and reset control
* Multi-tasking using FreeRTOS
* Thread-safe shared state using mutex

---

## Hardware Setup

### Pins

| Function     | GPIO |
| ------------ | ---- |
| Analog input | 34   |
| Relay        | 18   |
| Buzzer (PWM) | 19   |

### Voltage Divider

* R1 = 30 kΩ
* R2 = 7.5 kΩ

Voltage calculation:

```
Vin = Vout × ((R1 + R2) / R2)
```

---

## Overvoltage Logic

* Threshold: **5.0 V**
* When exceeded:

  * Relay is turned OFF immediately
  * Overvoltage state is latched
  * Alarm melody is played
* System stays latched until reset via web UI

---

## FreeRTOS Tasks

| Task          | Function                                        |
| ------------- | ----------------------------------------------- |
| TaskMonitor   | ADC reading, voltage calculation, relay control |
| TaskAlarm     | Plays alarm melody when latched                 |
| TaskTelemetry | Serial debugging output                         |
| TaskWeb       | Handles HTTP requests                           |

A mutex protects shared data across tasks.

---

## Web API

| Endpoint | Description             |
| -------- | ----------------------- |
| `/`      | Web dashboard           |
| `/data`  | JSON telemetry          |
| `/reset` | Clear overvoltage latch |

Example response:

```json
{
  "voltage": 4.98,
  "latch": false,
  "relay": "ON"
}
```

---

## Notes

* Uses `vTaskDelay()` instead of `delay()`
* `loop()` is deleted to rely fully on FreeRTOS tasks
* Relay is active by default and fails safe on overvoltage
* Designed for educational and prototyping use

