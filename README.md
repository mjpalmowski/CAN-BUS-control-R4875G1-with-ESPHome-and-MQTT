# Project Overview

![Huawei R4875G1 module in service](https://github.com/user-attachments/assets/8f0610da-cef0-4304-b7f7-ccc53e442615)

Up-cycling industrial equipment provides a sustainable way to reuse highly reliable components. Mobile-network cell-tower gear is built for 24 × 7 operation in harsh environments, so it is compact, efficient and remarkably resilient.

![R4875G1 on test bench](https://github.com/user-attachments/assets/448e4e05-493c-419a-ae62-c0d2bb7430c3)

This project centres on the **Huawei R4875G1 48 V Rectifier Module**. Designed to power base-station loads and battery banks, it can be trimmed from 45 V to 58 V and happily tolerates a “dirty” AC generator or weak grid. The module is **97 % efficient**, delivers up to **4 kW**, includes short-circuit and surge protection, speaks **CAN bus**, and boasts an MTBF of **500 000 h**. 

**A well-designed and powerful battery charger.**

[View the R4875G1 datasheet](https://github.com/user-attachments/files/17574446/R4975G1.Datasheet.pdf)

The ESPHome firmware presented here runs on an **ESP32** development board linked to the rectifier through a **VP230 (SN65HVD230) CAN-bus transceiver**.

---

## Key Features

NEW: MQTT support in web-app, see latest release.

### Supported Hardware
- **R4875G**
- **R4850G**
- **R4830G**

### Parallel Operation (up to three r48xx units, one ESP32 controller)
1. Join **CAN-H** lines  
2. Join **CAN-L** lines  
3. Wire **DC outputs** in parallel  

### Supported Sensors
- **Power State — ON / Hibernate / Error**
- **AC Power In**
- **DC Power Out**
- **Grid Frequency**
- **Input Current**
- **Output Current**
- **Input Grid Voltage**
- **Output Voltage**
- **Set Maximum Output Current**
- **Set Maximum Output Voltage**
- **Output Temperature**
- **FAN RPM** *(model-dependent)*
- **Total Operating Hours**

### Configuration Settings
- **CAN Voltage Set**
- **CAN Amp Set**
- **Fallback Amp Set**
- **Fallback Voltage Set**
- **Simple Daily Charge Timer**
- **CAN Max AC Amp Set** *(model-dependent)*
- **Cooling-Fan PWM Control** *(model-dependent)*
- **Low / High Voltage Set** (auto wake / hibernate)

### Control Buttons
- **CAN ON** (wake-up)
- **CAN OFF** (hibernate)
- **Fan Full Speed**
- **Fan Auto Mode**

### Additional Features
- **Over-temperature Shutdown** — YAML-configurable, no Home Assistant required  
- **Daily kWh Energy Meter** supplied DC energy  
- **Board-Type Auto-Detect**  
- **Manufacturing-Date Auto-Detect**  
- **Serial-Number Auto-Detect**  
- **Scaling-Factor Auto-Set**

### 🔋 Soft-Charge *(balance charging)*
- Charger tapers max current smoothly to avoid high-current BMS trips  
- Balance phase current **and** duration are adjustable  
- Auto-hibernate once balancing completes  

| Conventional charging of an unbalanced pack | Soft-Charge of an unbalanced pack |
| --- | --- |
| Full current right up to 100 % SOC → repeated disconnects | Current tapers, e.g. 25 A → 15 A → 10 A → 2 A |
| Cells get only minutes of balance time | Final stage at e.g. 0.5 A for up to 300 min |
| Bad vibes & stress to BMS and Battery | Safely restore pack balance |

The voltages in the "soft-charge" section act as triggers that reduce the charge current when the battery approaches full capacity.

The actual final charge voltage is set here: "10 Set Voltage Limit".

For example, if you set 54.75V as your "Set Voltage Limit", the charger will work towards that voltage at the configured current (12 Set DC Current Limit).

As the output voltage approaches the steep end of the voltage curve, it triggers the "soft-charge" algorithm at the specified voltages to gradually reduce the charge current.

The final trigger also starts a timer, allowing you to define how long the battery should remain in the balancing phase. Once that period has elapsed, the charger switches off.

It is important to note that drawing power from the battery during soft charge is not ideal. While it won’t damage the battery, it is recommended to disable soft-charge if the battery is in use while charging.

For optimal results, perform soft charging when the battery is not under load.

---


![Screenshot 2025-06-04 205431](https://github.com/user-attachments/assets/71c405c2-0ec9-4059-be26-4d138a0b29a6)



## Use Cases

Ideal for 15 s/16 s LiFePO₄ and 14 s NMC home battery systems, generator-backed solar, fast e-bike charging, marine DC systems and even clean-supply HAM-radio amplifiers. [Turn your ESS into a double conversion UPS and achieve true zero grid backfeed](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/discussions/4). Eliminate the need for a transfer switch. [Here is Node-RED flow that modulates R48XX charge power depending on surplus solar power.](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/discussions/8)

Commercial products such as the **EG4 Chargeverter** are essentially two telecom rectifiers in a box—this project offers an open, automation-friendly alternative.

---

## Hardware Setup

![Connection overview](https://github.com/user-attachments/assets/c70dea2a-3bfe-4446-9f6f-ea634a3ef5c6)

**Bill of materials**

| Item | Notes |
| --- | --- |
| ESP32-WROOM dev board | On-board CAN controller |
| VP230 (SN65HVD230) CAN-bus transceiver | 3.3 V logic |
| R4875G1/G5 edge connector | Available via AliExpress |
| 8 AWG silicone DC leads | 40 A continuous |
| 16 A-rated AC leads | 230 V single-phase |
| CAN bus twisted pair | White = CAN-L, Black = CAN-H |

### Powering the ESP32
Supply via USB or a 60 V → 5 V buck regulator if the battery is permanently connected.

---

### Quick Start
Flash the latest pre-compiled **.bin** from the [releases page using the ESPHome web-flasher, power up, and follow the on-device wizard.](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/releases/tag/v0.98)

---

## Manual Pad-Jump Start (without adapter board)

![Edge-connector pinout](https://github.com/user-attachments/assets/ae93452b-a830-4199-a4b6-3352d014ca55)

- **Pin 1 → Pin 5**  
- **Pin 1 → Pins 11 & 12** (connect Pin 11 and Pin 12 to DC-)  
- **Pin 9 ↔ Pin 10** connecting these two pins enables the full **75 A** limit (otherwise limited to 53 A)

[PCB adapter reference](https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/post-1732290)

---

## Software Configuration

Create a new ESPHome device in Home Assistant (or use standalone web control). Uncomment the **mqtt:**, **api:** or **webserver:** blocks as required. Multi-unit and three-phase YAML examples are provided in the repo.

Latest YAML sample: **[v0.98.YAML](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/blob/main/v0.98.yaml)**

2x R4875G1 YAML sample: **[R48xx_autoSet_fullFanCTRL_AC_currentCTRL.YAML](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/blob/main/R48xx_autoSet_fullFanCTRL_AC_currentCTRL.YAML)**

[Ready-compiled .BIN files to upload to the ESP32 are here.](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/releases/tag/v0.98)

If you are totally off-grid, no wifi, no cellular connection, no internet, use [this version.](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/discussions/9)

---

## MQTT Topics

<details>
<summary>Click to expand topic listings</summary>

### Text Sensors
`r48xx-off-grid/sensor/003_power_state/state` — Charger ON/OFF/Error  
`can-bus01/text_sensor/charger_power_state2/state`

### Sensors  
`can-bus01/sensor/ac_power_in/state` — AC Power In  
`can-bus01/sensor/dc_power_out/state` — DC Power Out  
*…and many more, see full list in repository*

### Numbers  
`can-bus01/number/can_voltage_set/state` — Voltage Set  
`can-bus01/number/can_amp_set/state` — Current Set  
*etc.*

### Buttons (send the string **PRESS** to command topic)  
`can-bus01/button/001_can_on_button/command` — Wake-up  
`can-bus01/button/002_can_off_button/command` — Hibernate  
`can-bus01/button/fan_full_speed_button/command` — Fan 100 %

### Number Commands  
`can-bus01/number/10_set_voltage_limit/command` — 49 – 58 V  
`can-bus01/number/14_set_ac_current_limit/command` — 0 – 21 A  
*etc.*

</details>

---

## Additional Resources
- [Huawei R4875G1 CAN protocol spreadsheet](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/blob/main/Protocol_R4875g.xlsx)
- [Beyond Logic review of Huawei rectifiers](https://www.beyondlogic.org/review-huawei-r4850g2-power-supply-53-5vdc-3kw/)
- [DIY Solar Forum discussion](https://diysolarforum.com/threads/diy-chargenectifier.56329/)
- Full user-manual PDFs for **R4875G1/G5, R4850G2, R4830G2** are linked in the repository.

