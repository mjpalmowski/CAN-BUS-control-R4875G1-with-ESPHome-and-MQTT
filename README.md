# Project Overview

![Screenshot 2024-11-29 173604](https://github.com/user-attachments/assets/8f0610da-cef0-4304-b7f7-ccc53e442615)



Upcycling industrial equipment offers a sustainable approach to reusing highly reliable components. Mobile network operators' cell tower equipment is built to operate 24/7 under harsh conditions, making it compact, efficient, and resilient.


![Screenshot 2025-01-27 171544](https://github.com/user-attachments/assets/448e4e05-493c-419a-ae62-c0d2bb7430c3)



This project focuses on the **Huawei R4875G1 48V Rectifier Module**, originally designed to power cell tower equipment and battery banks with dependable DC power. Essentially, this is a robust battery charger that can be adjusted between 45V and 58V, powered by an unstable grid or a dirty AC generator input. The R4875G1 module boasts a "Name Plate Output" of 4000W and an impressive Mean Time Between Failures (MTBF) of 500,000 hours. It supports current adjustments, dynamic control, and features hibernation and wake-up functions.

[View the R4975G1 Datasheet](https://github.com/user-attachments/files/17574446/R4975G1.Datasheet.pdf)

This module is 97% efficient, provides up to 4kW of DC power, and includes short circuit and surge protection, communicating via CAN BUS. These capabilities make it suitable for home battery charging, achieved [through reverse-engineering its CAN BUS control.](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/discussions/4)

## Key Features
The ESPHome firmware for this project is built for an ESP32 development board, which natively supports CAN BUS. **[The ESP32](https://www.aliexpress.com/w/wholesale-esp32-wroom-32.html) connects to the R48xx via a [VP230 CAN-BUS transceiver](https://www.aliexpress.com/w/wholesale-sn65hvd230-can-bus.html)**. Below are the main features:

### Supported Hardware:
- **R4875G**
- **R4850G**
- **R4830G**

### Up to three units are supported to run in parallel with one controller:

   **1) tie CAN-H bus lines together**
   
   **2) tie CAN-L lines together**
   
   **3) parallel connect DC-output**
   

### Supported Sensors
- **Power State ON/Hibernate/Error**
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
- **FAN-RPM** (NEW) (not available on all models)
- **Total Operating Hours** (NEW)


### Configuration Settings
- **CAN Voltage Set**
- **CAN Amp Set**
- **Fallback Amp Set**
- **Fallback Voltage Set**
- **Simple Daily Charge Timer**
- **CAN MAX AC Amp Set** (NEW) (not available on all models)
- **Cooling FAN PWM Control** (NEW) (not available on all models)
- **LOW and HIGH Voltage Set for auto Wake/Hibernate**

### Control Buttons
- **CAN ON Button** (Wake-up feature)
- **CAN OFF Button** (Hibernate feature)
- **Fan Full Speed Button**
- **Fan Auto Mode Button**

### Additional Features
- **Over-temperature Shutdown** (Configurable in YAML, does not require Home Assistant or Node-RED)
- **Daily kWh Energy Meter** for AC input and DC output, monitors energy consumption
- **Board-Type autodetect** (Detects specific R48xxGx variety)
- **Manufacturing Date autodetect**
- **Serial Number autodetect**
- **Scaling Factor auto-set**

### 🔋 Soft-Charge [Staged Charging]  (NEW)

* **No more “BMS high-current disconnect” surprises** – the charger eases back smoothly instead of hitting the BMS cut-off when a single cell goes over-voltage.  
* **Adjustable balance phase** – set both the *current* and the *duration* of the final stage to match your pack’s balancing capability.  
* **Auto-hibernate** – the charger switches off automatically once the balancing period has finished.  

| Without Soft-Charge                                                        | Soft-Charge                                                                                          |
|---------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| Charger delivers full current as battery approaches FULL → risk of repeated high current disconnect | Current tapers, e.g. 25 A → 15 A → 10 A → 2 A, allowing the pack to reach 100 % SOC avoiding high current disconnect |
| Cells may receive only a few minutes of balance time                          | Final stage runs at e.g. 0.5 A max for up to 300 min, giving the balancers time to work                 |



## Use Cases
This setup is ideal for charging common 15s and 16s LiFePO4 packs and 14s NMC batteries, making it useful for home battery systems that store solar or off-peak power. It is particularly useful when backing up solar setups with a generator. Additionally, it can serve as a powerful DC bench power supply when paired with a robust [adjustable](https://github.com/mjpalmowski/esphome-juntek-DPM8650-mqtt-http-tool) buck/boost converter, as an onboard fast-charger for boats that encounter different supply voltages or as a rapid charger for Ebikes, [here is a guy that runs his HAM radio amp equippment](https://qsl.net/zl1rs/old/r4875g1.html) with it (clean DC indeed). And for those who want to play along at home, here is a guy that has an innovative approach to use four R4875G1s to create a double conversion system (he is basically planning to run his entire grid import through these rectifiers straight to his batteries, so as far as the grid is concerned he is just using a battery charger, very cool), removing the need for clunky high current relays (changeover switches, ATS), removing the worry about grid backfeed. [His story is unfolding here](https://diysolarforum.com/threads/diy-chargenectifier.56329/page-29#post-1281632).  
If you have a large solar install (string inverters or microinverters) without batteries and would like to add a battery on the cheap and realize zero export have a look at the [NodeRed flow that controls the charge power depending on the grid export.](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/discussions/8) 

For example, the **EG4 Chargeverter** product is essentially two of these 48V telecom units in a box, with buttons for setting maximum voltage and current. [Watch the Chargeverter Teardown](https://www.youtube.com/watch?v=WPEjRtABc2U).

So, if you want to build an automation-friendly Chargeverter alternative, you are in the right place.

## Hardware Setup

### Module and Connections
The **Huawei R4875G1** power module is controlled as shown below. 


PCB edge connectors for the R4875G1 and R4875G5 series are available on AliExpress.

![Designer](https://github.com/user-attachments/assets/c70dea2a-3bfe-4446-9f6f-ea634a3ef5c6)


- **ESP32 WROOM** Development Board
- **VP230** CAN-BUS transceiver Board (SN65HVD230)
- **R4875G1/G5 Board Edge Connector**
- **DC Cables**: 8 AWG silicone-insulated, supporting up to 40A continuously.
- **AC Cables**: Rated for continuous 16A supply.
- **CAN BUS Connections**: White (CAN-L) and Black (CAN-H)


### Power supply

You can supply the ESP32 using a USB cable, or if your Battery is allways connected, you can also use a buck converter:

Google Search: [DC-DC 60V 5V 2A Buck Regulator](https://www.aliexpress.com/w/wholesale-dc-dc-converter-step-down-60v-to-5v.html)

![Screenshot 2025-03-07 110516](https://github.com/user-attachments/assets/c5579568-974c-4958-9d7e-8400ec5f987c)



To connect, ensure the TX pin from the ESP32 is connected to the transceiver's TX pin, and the RX pin is connected to the transceiver's RX pin.

**Now, you have it all connected and can't wait to try? Go here [set it up as in the quick start guide for the ready made .bin](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/releases/tag/v0.97)** 








## For those not using the adapter board, the module can be manually turned on by shorting specific pads to DC-minus
### Important Detail about the Edge Connector/Pins

![Screenshot 2024-11-29 152930](https://github.com/user-attachments/assets/ae93452b-a830-4199-a4b6-3352d014ca55)

Connect the following pins together:
***Connect Pin1 to Pin5***

Connect the following Pins to DC- (Pin1):
***Connect Pin1 to Pin11, connect Pin1 to Pin12***

Connect the folowing pins together (this will allow the full 75A max output currrent to be set):
***Connect Pin9 to Pin10*** 

- If Pin9 and Pin10 are not connected the R4875G can only be set to a maximum of 50A.
  
- If Pin9 is connected to Pin10 the R4875G can be stet to a max of 75A.

### Hardware Reference:
- [PCB Adapter Guide](https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/post-1732290)

## Software Configuration

You can use the **ESPHome** plugin in Home Assistant to create a new device and paste the provided YAML configuration. 
If you prefer a standalone setup, you can configure the web server component for browser based control.

1. **MQTT**: Uncomment the `mqtt:` block if using MQTT.
2. **Home Assistant API**: Uncomment the `api:` block for Home Assistant discovery.
3. **Web Interface**: Uncomment the `webserver:` block if a web interface is needed.

If you are looking to [go BIG](https://www.youtube.com/watch?v=OHAXydKthXM), or go [three phase](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/blob/main/3-phase-R48xxGx.yaml) hooking several units to your CAN BUS allowing to set values for all units simultaneously and receive the sensor data separately.

**Here is the latests YAML example to [control up to two R48xxGx units on one can-bus via MQTT (have to be of identical type) but this version has auto "SETUP" Board-Type detection it has AC-Current limit-set and PWM FAN control.](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/blob/main/R48xx_autoSet_fullFanCTRL_AC_currentCTRL.YAML)**


# Pre-compiled `.BIN` files are available for direct upload to an ESP32 board if you do not wish to modify the YAML file:

[latest release 0.9.7 with "Soft-Charge"](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/releases/tag/v0.97)

https://web.esphome.io/?dashboard_wizard to upload the .bin

***[The-zombies-are-already-here, Total Off-Grid, Preppers, SHTF: This way](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/releases/tag/v001-SHTF-offgrid-edition)*** to a version of the software to control Huawai R48xxGx that creates a Hotspot on the ESP32 to which you can directly connect with your phone and control the charger. No WiFi network, no Internet, no Home Assistant, all you need is your phone. This version works for all Huawai R4830G, R4850G and R4875G devices there is a SETUP button on the web-app that discovers the amp rating.

### Example Screenshots

![Screenshot 2024-12-17 230917](https://github.com/user-attachments/assets/7e92dae8-5b62-417b-a34f-55a1e8e75657)


![web surface intigration](https://github.com/user-attachments/assets/8f8a1d15-0d97-484c-a825-1dc12245aaed)

![Screenshot 2024-11-14 153811](https://github.com/user-attachments/assets/4d047509-b9c0-49b8-b7c5-9a71261ee79a)

![Screenshot 2024-11-14 150637](https://github.com/user-attachments/assets/ff781577-ade7-4bd4-8a26-ba679e6a9398)

![Screenshot 2024-11-14 150252](https://github.com/user-attachments/assets/fcf0bd69-1b4e-453c-a8c8-5b66df1f6714)

![Screenshot 2024-11-20 140636](https://github.com/user-attachments/assets/6f592d96-79e6-4d91-bfcd-1ca8331c07e0)

![Screenshot 2024-11-20 144815](https://github.com/user-attachments/assets/bfa286e8-b760-467e-8e03-3064d890d0f9)

![Screenshot 2024-11-20 140157](https://github.com/user-attachments/assets/25eee449-0177-4e5f-9a8c-a76bbffcef38)

# MQTT Topics

## Binary Sensors
- **Charger Power State ON/Hibernate**
  - State Topic: `can-bus01/binary_sensor/charger_power_state/state`
- **Charger Power State2**
  - State Topic: `can-bus01/binary_sensor/charger_power_state2/state`

## Sensors
- **Output Current alt**
  - State Topic: `can-bus01/sensor/output_current_alt/state`
- **Output Current alt2**
  - State Topic: `can-bus01/sensor/output_current_alt2/state`
- **AC Power In**
  - State Topic: `can-bus01/sensor/ac_power_in/state`
- **DC Power Out**
  - State Topic: `can-bus01/sensor/dc_power_out/state`
- **Grid Frequency**
  - State Topic: `can-bus01/sensor/grid_frequency/state`
- **Input Current**
  - State Topic: `can-bus01/sensor/input_current/state`
- **Output Voltage**
  - State Topic: `can-bus01/sensor/output_voltage/state`
- **Input Grid Voltage**
  - State Topic: `can-bus01/sensor/input_grid_voltage/state`
- **Output Temperature**
  - State Topic: `can-bus01/sensor/output_temperature/state`
- **Output Current**
  - State Topic: `can-bus01/sensor/output_current/state`
- **Set Max Output Current**
  - State Topic: `can-bus01/sensor/set_max_output_current/state`
- **AC Power In2**
  - State Topic: `can-bus01/sensor/ac_power_in2/state`
- **Combined AC Power**
  - State Topic: `can-bus01/sensor/combined_ac_power/state`
- **DC Power Out2**
  - State Topic: `can-bus01/sensor/dc_power_out2/state`
- **Combined DC Power**
  - State Topic: `can-bus01/sensor/combined_dc_power/state`
- **AC Power In Total (kWh)**
  - State Topic: `can-bus01/sensor/ac_power_in_total__kwh_/state`
- **DC Power Out Total (kWh)**
  - State Topic: `can-bus01/sensor/dc_power_out_total__kwh_/state`
- **Grid Frequency2**
  - State Topic: `can-bus01/sensor/grid_frequency2/state`
- **Input Current2**
  - State Topic: `can-bus01/sensor/input_current2/state`
- **Output Voltage2**
  - State Topic: `can-bus01/sensor/output_voltage2/state`
- **Input Grid Voltage2**
  - State Topic: `can-bus01/sensor/input_grid_voltage2/state`
- **Output Temperature2**
  - State Topic: `can-bus01/sensor/output_temperature2/state`
- **Output Current2**
  - State Topic: `can-bus01/sensor/output_current2/state`
- **Set Max Output Current2**
  - State Topic: `can-bus01/sensor/set_max_output_current2/state`

## Numbers
- **CAN Voltage Set**
  - State Topic: `can-bus01/number/can_voltage_set/state`
- **CAN Amp Set**
  - State Topic: `can-bus01/number/can_amp_set/state`
- **Fallback Amp Set**
  - State Topic: `can-bus01/number/fallback_amp_set/state`
- **Fallback Voltage Set**
  - State Topic: `can-bus01/number/fallback_voltage_set/state`

## Buttons

How to use: send Text string: `PRESS` to activate

- **CAN ON Button**
  - State Topic: `can-bus01/button/can_on_button/state
  - Command Topic: `can-bus01/button/can_on_button/command`
- **CAN OFF Button**
  - State Topic: `can-bus01/button/can_off_button/state`
  - Command Topic: `can-bus01/button/can_off_button/command`
- **Fan Full Speed Button**
  - State Topic: `can-bus01/button/fan_full_speed_button/state`
  - Command Topic: `can-bus01/button/fan_full_speed_button/command`
 



## Text Sensor Topics

How to use: The G4857G1 listens to the following MQTT topics. Publishing to these topics; integers (for AMP settings) or floating-point numbers (for Volt settings) will set the respective values.

- **set_fan_speed_percentage**
  - **Topic:** `can-bus01/number/set_fan_speed_percentage/command`
  - **Description:** Sets the fan speed as a percentage (0–100%).

- **10_set_voltage_limit**
  - **Topic:** `can-bus01/number/10_set_voltage_limit/command`
  - **Description:** Configures the voltage limit (49–58 V).

- **11_set_fallback_voltage_limit**
  - **Topic:** `can-bus01/number/11_set_fallback_voltage_limit/command`
  - **Description:** Configures the fallback voltage limit (49–58 V).

- **14_set_ac_current_limit**
  - **Topic:** `can-bus01/number/14_set_ac_current_limit/command`
  - **Description:** Sets the AC current limit (0–21 A).

- **12_set_dc_current_limit**
  - **Topic:** `can-bus01/number/12_set_dc_current_limit/command`
  - **Description:** Sets the DC current limit (1–75 A).

- **13_set_fallback_dc_current_limit**
  - **Topic:** `can-bus01/number/13_set_fallback_dc_current_limit/command`
  - **Description:** Sets the fallback DC current limit (1–75 A).




# Additional Resources
- [Huawei R4875G1 CAN Protocol](https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/blob/main/Protocol_R4875g.xlsx)
- [Beyond Logic Review](https://www.beyondlogic.org/review-huawei-r4850g2-power-supply-53-5vdc-3kw/)
- [Endless Sphere Forum](https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/)
- [DIY Solar Forum Discussion](https://diysolarforum.com/threads/diy-chargenectifier.56329/)
- [R4875G1 Rectifier User Manual.pdf](https://github.com/user-attachments/files/18236214/R4875G1.Rectifier.User.Manual.pdf)
- [R4875G5 Rectifier User Manual V2.3.pdf](https://github.com/user-attachments/files/17949610/R4875G5.Rectifier.User.Manual.V2.3.pdf)
- [R4850G2 Rectifier User Manual.pdf](https://github.com/user-attachments/files/18236128/R4850G2.Rectifier.User.Manual.pdf)
- [R4830G2 Rectifier User Manual.pdf](https://github.com/user-attachments/files/18236076/R4830G2.Rectifier.User.Manual.pdf)


