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
- **Fan Full Speed Button** (Triggers full fan speed at a specific temperature)
- **Fan Auto Mode Button**

### Additional Features
- **Over-temperature Shutdown** (Configurable in YAML, does not require Home Assistant or Node-RED)

## Use Cases
This setup is ideal for charging common 15s and 16s LiFePO4 packs and 14s NMC batteries, making it perfect for home battery systems to store solar or off-peak power. It is particularly useful when backing up solar setups with a generator. Additionally, it can serve as a powerful DC bench power supply when paired with a robust adjustable buck converter, or even as an onboard e-bike charger.

For example, the **EG4 Chargeverter** product is essentially two of these 48V telecom units in a single box, with buttons for setting maximum voltage and current. [Watch the Chargeverter Teardown](https://www.youtube.com/watch?v=WPEjRtABc2U).

## Hardware Setup

### Module and Connections
The **Huawei R4875G1** power module is controlled as shown below. It can output 35A at 54V continuously at an ambient temperature of 23°C without additional cooling. PCB contact adapters for the R4875G1 and R4875G5 series are available on AliExpress.

![Screenshot 2024-11-14 103724](https://github.com/user-attachments/assets/5043e65a-0674-4dff-8a67-0821f1380e3c)

- **DC Cables**: 8 AWG silicone-insulated, supporting up to 40A continuously.
- **AC Cables**: Rated for continuous 16A supply.
- **CAN BUS Connections**: White (CAN-L) and Black (CAN-H) Dupont cables.

![Module Diagram](https://github.com/user-attachments/assets/68757a31-27ee-47f2-8b01-ca278633819a)

To connect, ensure the TX pin from the ESP32 is connected to the transceiver's TX pin, and the RX pin is connected to the transceiver's RX pin.

![Wiring Diagram](https://github.com/user-attachments/assets/4670849b-ee3d-4f3b-bfe2-2639171bf4d3)

For those not using the adapter board, the module can be manually turned on by shorting specific pads to DC-minus.

### Hardware References
- [PCB Adapter Guide](https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/post-1732290)
- [YouTube Hardware Overview](https://www.youtube.com/watch?v=yvtQGEbZ6_c)

## Software Configuration

You can use the **ESPHome** plugin in Home Assistant to create a new device and paste the provided YAML configuration. If you prefer a standalone setup, you can configure a web server for device management without Home Assistant.

1. **MQTT**: Uncomment the `mqtt:` block if using MQTT.
2. **Home Assistant API**: Uncomment the `api:` block for Home Assistant discovery.
3. **Web Interface**: Uncomment the `webserver:` block if only a web interface is needed.

Pre-compiled `.bin` files are available for direct upload to an ESP32 board if you do not wish to modify the YAML file.

### Example Screenshots
![Web Interface](https://github.com/user-attachments/assets/7bb592bb-c210-440f-ad3e-b862299c11ee)
![Home Assistant Integration](https://github.com/user-attachments/assets/0c11c5cd-dbff-4e06-8870-e32d41f5d8e9)

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
- **CAN ON**: `can-bus01/button/can_on_button/command`
- **CAN OFF**: `can-bus01/button/can_off_button/command`
- **Fan Speed Control**: Auto and Full Speed via MQTT

## Additional Resources
- [Huawei R4875G1 CAN Protocol](https://github.com/user-attachments/files/17571999/Protocol_R4875g.xlsx)
- [Beyond Logic Review](https://www.beyondlogic.org/review-huawei-r4850g2-power-supply-53-5vdc-3kw/)
- [Endless Sphere Forum](https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/)
- [DIY Solar Forum Discussion](https://diysolarforum.com/threads/diy-chargenectifier.56329/)
