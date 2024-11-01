# Background: 
Upcycling industrial equipment is an appealing approach for several reasons. Industrial equipment used in mobile network operators' cell towers is designed to run 24/7 under harsh conditions, making it compact, reliable, efficient, and resilient.

This project focuses on a "48V Rectifier module" used to supply cell tower equipment and battery banks with dependable DC power. Essentially, it's a heavy-duty battery charger that can be set between 45V and 58V while powered by dirty AC generator input or an unstable grid. The R4875G1 module has a "Name Plate Output" of 4000W and an impressive "Mean Time Between Failures" (MTBF) of 500,000 hours. Current levels can also be adjusted and dynamically controlled, and the module features hibernation and wake-up functions.

[R4975G1 Datasheet.pdf](https://github.com/user-attachments/files/17574446/R4975G1.Datasheet.pdf)


This module is 97% efficient and can operate almost silently if run at 25% of max output whilst providing 1000W DC power. Such specs have led many, including myself, to make these units usable for home battery charging by reverse-engineering their CAN BUS control.

So here is my 20cents:

The ESPHome firmware below is for an ESP32 dev board, which natively supports CAN BUS. Here’s a quick summary of what is needed to make it work.

# Use Case:
This is a battery charger for common 15s and 16s LiFePO4 packs and 14s NMC batteries. It’s ideal for home battery setups used to store solar or off-peak power, as it enables quick battery top-ups. If you back up your solar setup with a generator, these units serve well between the generator and a large battery pack since they’re designed for that purpose. They’re also a great starting point for a bench DC power supply when combined with a robust adjustable buck converter. Due to their power density, some also use them as onboard e-bike chargers.

When used as a battery charger, there’s a product called the "EG4 Chargeverter," essentially two of these telecom units packed into a box with buttons to set max voltage/current. Here’s a teardown showing two standard telecom 48V supplies:

https://www.youtube.com/watch?v=WPEjRtABc2U

If you’re interested in building an "automation-friendly" version, keep reading.


# Hardware: 
![IMG_20241018_141436](https://github.com/user-attachments/assets/c75316e2-48f6-43c9-b544-35b82bb796bc)
ESP32 board with TJA1050 CAN-BUS transceiver board (requires a level shifter, but this setup worked).

![IMG_20241018_140009](https://github.com/user-attachments/assets/68757a31-27ee-47f2-8b01-ca278633819a)


This is the Huawei R4875G1 power module we're controlling. The arrows indicate airflow direction inside the unit. If operated at 23°C ambient, it can output 20A at 54V continuously without additional cooling or overheating. The PCB contact adapter for the R4875G1 and R4875G5 series is available on AliExpress.

![IMG_20241018_140546](https://github.com/user-attachments/assets/26adaf1d-337f-4390-b8ca-50e13a5c1673)

DC cables are 8AWG silicone insulated, capable of carrying 40A continuously. AC cables are sized to supply 16A continuously. The white Dupont cable is CAN-L, and the black Dupont cable is CAN-H. The other two small wires connect to DC NEG.

![IMG_20241018_140743](https://github.com/user-attachments/assets/ea1e3ff2-27fd-47af-94fe-055ffd697be8)

TJA1050 CAN-BUS transceiver board


![IMG_20241018_141733](https://github.com/user-attachments/assets/909a2692-d080-4f7c-9965-e06ca748c99c)

This connects the 5V logic level from the CAN transceiver module to the 3.3V ESP32 input pin. My ESP32 handled the 5V input (though it’s not designed for that), and the transceiver managed the 3.3V logic input from the ESP32. However, you should ideally add a logic level shifter between the ESP32 and the transceiver module.

If you don’t want to buy the adapter board, you can turn on the module by shorting these pads to DC-minus:

![Screenshot 2024-10-18 164741](https://github.com/user-attachments/assets/edd97e21-da8d-49c3-851b-2305f1d71256)

![Screenshot 2024-10-18 165143](https://github.com/user-attachments/assets/8a0f7d83-c754-46e7-8dd0-eef3c1ae49cb)

To unlock the full 4kW of DC output, follow this guide:

https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/post-1732290

# Hardware References:

https://www.youtube.com/watch?v=yvtQGEbZ6_c

https://www.youtube.com/watch?v=Qu5-XbeGiYY

[ZL1RS Bob Sutton New Zealand - Huawei R4875G1 SMPSU.pdf](https://github.com/user-attachments/files/17571985/ZL1RS.Bob.Sutton.New.Zealand.-.Huawei.R4875G1.SMPSU.pdf)


The free opposing pair of contacts (of the three contacts in the center) are your CAN-BUS pins. CAN-High is on the bottom, and CAN-L is at the top:

![can](https://github.com/user-attachments/assets/abf646de-7ed0-40bd-977f-927654330967)

# Software:

You can use Home Assistant’s ESPHome plugin to add a new ESPHome device and insert the code from the attached YAML file, "CAN-R4875G1-ESP32.YAML" (copy and paste everything below "captive_portal:" into your new ESPHome device YAML).

If you don’t need MQTT, comment out the "mqtt:" block.
If you don’t need Home Assistant, comment out the "api:" block.
If you keep the "api:" block active, the device will be discovered by Home Assistant upon restart, appearing like this:

![Screenshot 2024-10-18 170221](https://github.com/user-attachments/assets/00b6da9a-1fe3-4be9-9083-7ba2df3a7ec5)

![Screenshot 2024-10-18 170149](https://github.com/user-attachments/assets/c8e686f8-5a49-41f0-8d6d-133be1017357)

MQTT topics are as follows:

MQTT Sensor 'AC Power In': State Topic: 'can-bus01/sensor/ac_power_in/state'
MQTT Sensor 'DC Power Out': State Topic: 'can-bus01/sensor/dc_power_out/state'
MQTT Sensor 'Grid Frequency': State Topic: 'can-bus01/sensor/grid_frequency/state'
MQTT Sensor 'Input Current': State Topic: 'can-bus01/sensor/input_current/state'
MQTT Sensor 'Output Voltage': State Topic: 'can-bus01/sensor/output_voltage/state'
MQTT Sensor 'Set Max Output Current': State Topic: 'can-bus01/sensor/set_max_output_current/state'
MQTT Sensor 'Input Grid Voltage': State Topic: 'can-bus01/sensor/input_grid_voltage/state'
MQTT Sensor 'Output Temperature': State Topic: 'can-bus01/sensor/output_temperature/state'
MQTT Sensor 'Output Current': State Topic: 'can-bus01/sensor/output_current/state'
MQTT Number 'CAN Voltage Set': State Topic: 'can-bus01/number/can_voltage_set/state'
MQTT Number 'CAN Amp Set': State Topic: 'can-bus01/number/can_amp_set/state'
MQTT Number 'Fallback Amp Set': State Topic: 'can-bus01/number/fallback_amp_set/state'
MQTT Number 'Fallback Voltage Set': State Topic: 'can-bus01/number/fallback_voltage_set/state'
MQTT Button 'CAN ON Button': State Topic: 'can-bus01/button/can_on_button/state', Command Topic: 'can-bus01/button/can_on_button/command'
MQTT Button 'CAN OFF Button': State Topic: 'can-bus01/button/can_off_button/state', Command Topic: 'can-bus01/button/can_off_button/command'
MQTT Button 'Fan Full Speed Button': State Topic: 'can-bus01/button/fan_full_speed_button/state', Command Topic: 'can-bus01/button/fan_full_speed_button/command'
MQTT Button 'Fan Auto Mode Button': State Topic: 'can-bus01/button/fan_auto_mode_button/state', Command Topic: 'can-bus01/button/fan_auto_mode_button/command'

# Software references:

[Protocol_R4875g.xlsx](https://github.com/user-attachments/files/17571999/Protocol_R4875g.xlsx)

Adopted from: 

https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/post-1805865




