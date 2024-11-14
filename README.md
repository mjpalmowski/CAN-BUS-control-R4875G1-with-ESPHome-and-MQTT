# Project Overview

Upcycling industrial equipment offers a sustainable approach to reusing highly reliable components. Mobile network operators' cell tower equipment is built to operate 24/7 under harsh conditions, making it compact, efficient, and resilient.

This project focuses on the **Huawei R4875G1 48V Rectifier Module**, originally designed to power cell tower equipment and battery banks with dependable DC power. Essentially, this is a robust battery charger that can be adjusted between 45V and 58V, powered by an unstable grid or a dirty AC generator input. The R4875G1 module boasts a "Name Plate Output" of 4000W and an impressive Mean Time Between Failures (MTBF) of 500,000 hours. It supports current adjustments, dynamic control, and features hibernation and wake-up functions.

[View the R4975G1 Datasheet](https://github.com/user-attachments/files/17574446/R4975G1.Datasheet.pdf)

This module is 97% efficient, provides up to 4kW of DC power, and includes short circuit and surge protection, communicating via CAN BUS. These capabilities make it suitable for home battery charging, achieved through reverse-engineering its CAN BUS control.

## Key Features
The ESPHome firmware for this project is built for an ESP32 development board, which natively supports CAN BUS. The ESP32 connects to the R4875G1 via a VP230 CAN-BUS transceiver. Below are the main features:

### Supported Sensors
- **AC Power In**
- **DC Power Out**
- **Grid Frequency**
- **Input Current**
- **Output Voltage**
- **Set Maximum Output Current**
- **Input Grid Voltage**
- **Output Temperature**
- **Output Current**

### Configuration Settings
- **CAN Voltage Set**
- **CAN Amp Set**
- **Fallback Amp Set**
- **Fallback Voltage Set**

### Control Buttons
- **CAN ON Button** (Wake-up feature)
- **CAN OFF Button** (Hibernate feature)
- **Fan Full Speed Button**
- **Fan Auto Mode Button**

### Additional Features
- **Over-temperature Shutdown** (Configurable in YAML, does not require Home Assistant or Node-RED)

## Use Cases
This setup is ideal for charging common 15s and 16s LiFePO4 packs and 14s NMC batteries, making it useful for home battery systems that store solar or off-peak power. It is particularly useful when backing up solar setups with a generator. Additionally, it can serve as a powerful DC bench power supply when paired with a robust adjustable buck/boost converter, or even as an onboard e-bike charger.

For example, the **EG4 Chargeverter** product is essentially two of these 48V telecom units in a single box, with buttons for setting maximum voltage and current. [Watch the Chargeverter Teardown](https://www.youtube.com/watch?v=WPEjRtABc2U).

## Hardware Setup

### Module and Connections
The **Huawei R4875G1** power module is controlled as shown below. It can output 35A at 54V continuously at an ambient temperature of 23°C without additional cooling. 

![Screenshot 2024-11-14 111944](https://github.com/user-attachments/assets/c53ce6c9-9675-487b-8b2c-839d39cd7b22)


PCB contact adapters for the R4875G1 and R4875G5 series are available on AliExpress.

![Designer](https://github.com/user-attachments/assets/c70dea2a-3bfe-4446-9f6f-ea634a3ef5c6)


- **ESP32 WROOM** Development Board
- **VP230** CAN-BUS transceiver Board
- **R4875G1/G5 Board Edge Connector**
- **DC Cables**: 8 AWG silicone-insulated, supporting up to 40A continuously.
- **AC Cables**: Rated for continuous 16A supply.
- **CAN BUS Connections**: White (CAN-L) and Black (CAN-H)

To connect, ensure the TX pin from the ESP32 is connected to the transceiver's TX pin, and the RX pin is connected to the transceiver's RX pin.

![Wiring Diagram](https://github.com/user-attachments/assets/4670849b-ee3d-4f3b-bfe2-2639171bf4d3)

For those not using the adapter board, the module can be manually turned on by shorting specific pads to DC-minus.


![Screenshot 2024-11-14 111652](https://github.com/user-attachments/assets/7a3fbde8-20d1-4139-a639-aaebd0c17b3e)

Top side view

![Screenshot 2024-10-18 165143](https://github.com/user-attachments/assets/ba84a6bc-31b6-4bc0-912d-8995dfcbe027)

Bottom Side view (Details in the "YouTube Hardware Overview" link blow)



### Hardware References
- [PCB Adapter Guide](https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/post-1732290)
- [YouTube Hardware Overview](https://www.youtube.com/watch?v=yvtQGEbZ6_c)

## Software Configuration

You can use the **ESPHome** plugin in Home Assistant to create a new device and paste the provided YAML configuration. 
If you prefer a standalone setup, you can configure the web server component for browser based control.

1. **MQTT**: Uncomment the `mqtt:` block if using MQTT.
2. **Home Assistant API**: Uncomment the `api:` block for Home Assistant discovery.
3. **Web Interface**: Uncomment the `webserver:` block if a web interface is needed.

## Pre-compiled `.bin` files are available for direct upload to an ESP32 board if you do not wish to modify the YAML file:


https://github.com/mjpalmowski/CAN-BUS-control-R4875G1-with-ESPHome-and-MQTT/releases/tag/v0.9

https://web.esphome.io/?dashboard_wizard to upload the .bin
 


### Example Screenshots

![Web-Surface Integration](https://github.com/user-attachments/assets/4e74206d-bf3e-473a-ab9f-b4b039914179)

![Screenshot 2024-11-14 150637](https://github.com/user-attachments/assets/ff781577-ade7-4bd4-8a26-ba679e6a9398)

![Screenshot 2024-11-14 150252](https://github.com/user-attachments/assets/fcf0bd69-1b4e-453c-a8c8-5b66df1f6714)



### MQTT Topics
#### Sensors
- `can-bus01/sensor/ac_power_in/state`
- `can-bus01/sensor/dc_power_out/state`
- `can-bus01/sensor/grid_frequency/state`
- `can-bus01/sensor/input_current/state`
- `can-bus01/sensor/output_voltage/state`
- `can-bus01/sensor/output_temperature/state`
- `can-bus01/sensor/output_current/state`

#### Commands
- **wake-up**: `can-bus01/button/can_on_button/command`
- **hibernate**: `can-bus01/button/can_off_button/command`
- **fan auto**: `can-bus01/button/fan_auto_mode_button/command`
- **fan full speed**: `can-bus01/button/fan_full_speed_button/command`

## Additional Resources
- [Huawei R4875G1 CAN Protocol](https://github.com/user-attachments/files/17571999/Protocol_R4875g.xlsx)
- [Beyond Logic Review](https://www.beyondlogic.org/review-huawei-r4850g2-power-supply-53-5vdc-3kw/)
- [Endless Sphere Forum](https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/)
- [DIY Solar Forum Discussion](https://diysolarforum.com/threads/diy-chargenectifier.56329/)
