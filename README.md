# Background: 
Upcycling industrial equipment is an appealing approach for several reasons. Industrial equipment used in mobile network operators' cell towers is designed to run 24/7 under harsh conditions, making it compact, reliable, efficient, and resilient.

This project focuses on a "48V Rectifier module" used to supply cell tower equipment and battery banks with dependable DC power. Essentially, it's a heavy-duty battery charger that can be set between 45V and 58V while powered by dirty AC generator input or an unstable grid. The R4875G1 module has a "Name Plate Output" of 4000W and an impressive "Mean Time Between Failures" (MTBF) of 500,000 hours. Current levels can also be adjusted and dynamically controlled, and the module features hibernation and wake-up functions.

[R4975G1 Datasheet.pdf](https://github.com/user-attachments/files/17574446/R4975G1.Datasheet.pdf)


This module is 97% efficient and can providing up to 4KW of DC power, it is short circuit and surge protected and speaks CAN BUS. Such specs have led many, including myself, to make these units usable for home battery charging by reverse-engineering their CAN BUS control.

So here is my 20cents:

The ESPHome firmware below is for an ESP32 dev board, which natively supports CAN BUS. The ESP32 is hooked up to the R4875G via VP230 CAN-BUS transceiver board.  Here’s a quick summary of what is needed to make it work.

## Features supported:
### Sensors
- **AC Power In**  

- **DC Power Out**  

- **Grid Frequency**  

- **Input Current**  

- **Output Voltage**  

- **Set Max Output Current**  

- **Input Grid Voltage**  

- **Output Temperature**  

- **Output Current**  

### Settings
- **CAN Voltage Set**  

- **CAN Amp Set**  

- **Fallback Amp Set**  

- **Fallback Voltage Set**

### Buttons
- **CAN ON Button**  (wake-up feature)

- **CAN OFF Button**  (hibernate feature)

- **Fan Full Speed Button**  (allows for automation that triggers full fan speed at certain temperature range)

- **Fan Auto Mode Button** 

### Set Overtemp Shutdown in YAML (does not require HA or NodeRed to be active)
  

# Use Case:
This is a battery charger for common 15s and 16s LiFePO4 packs and 14s NMC batteries. It’s ideal for home battery setups used to store solar or off-peak power, as it enables quick battery top-ups. If you back up your solar setup with a generator, these units serve well between the generator and a large battery pack since they’re designed for that purpose. They’re also a great starting point for a bench DC power supply when combined with a robust adjustable buck converter. Due to their power density, some also use them as onboard e-bike chargers.

There’s a product called the "EG4 Chargeverter," The Chargeverter is essentially two of these 48V telecom units packed into a box with buttons to set max voltage/current. Here’s a teardown:

https://www.youtube.com/watch?v=WPEjRtABc2U

If you’re interested in building an "automation-friendly" version of this device, keep reading.


# Hardware: 


![IMG_20241018_140009](https://github.com/user-attachments/assets/68757a31-27ee-47f2-8b01-ca278633819a)

This is the Huawei R4875G1 power module we're controlling. The arrows indicate airflow direction inside the unit. If operated at 23°C ambient, it can output 35A at 54V continuously without additional cooling or overheating. The PCB contact adapter for the R4875G1 and R4875G5 series is available on AliExpress.

![IMG_20241018_140546](https://github.com/user-attachments/assets/26adaf1d-337f-4390-b8ca-50e13a5c1673)

DC cables are 8AWG silicone insulated, capable of carrying 40A continuously. AC cables are sized to supply 16A continuously. The white Dupont cable is CAN-L, and the black Dupont cable is CAN-H. The other two small wires connect to DC NEG.


![Screenshot 2024-11-14 103724](https://github.com/user-attachments/assets/79ad670a-10e9-404d-9aa6-95732e0d5277)


Make sure YAML defined TX pin is connected to the tranceiver TX pin.  YAML defined RX pin is connected to the tranceiver RX pin: 

![Screenshot 2024-11-11 093708](https://github.com/user-attachments/assets/4670849b-ee3d-4f3b-bfe2-2639171bf4d3)

### Here is a pic of the 3.3v CAN tranceiver that you should be using. it is called "SN65HVD230" also found under "VP230 CAN Board": 

![IMG_20241111_093108](https://github.com/user-attachments/assets/d07556bf-98fd-4b8a-a10a-a0638687308f)



If you don’t want to buy the adapter board, you can turn on the module by shorting these pads to DC-minus:

![Screenshot 2024-10-18 164741](https://github.com/user-attachments/assets/edd97e21-da8d-49c3-851b-2305f1d71256)

![Screenshot 2024-10-18 165143](https://github.com/user-attachments/assets/8a0f7d83-c754-46e7-8dd0-eef3c1ae49cb)

The "Hibernate" and "Wake-UP" Can-messages work just fine with these pads connected to DC-Neg.

### To unlock the full 4kW of DC output, follow this guide:

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

If you just want to have a web page to set up your R4875G1, then comment out the api and the mqtt section, enter your wifi credentials and uncomment the "webserver:" section. You can compile and upload the bin without ever installing Homeassistant also: https://www.youtube.com/watch?v=BX6tDsux_X4

If you just need the web interface and don't care about changing anything in the YAML file don't have or don't care about mqtt or Homeassistant, there is a .bin file that you can directly upload to your ESP32 board... instructions are provided in r4875g1-can-web.0.9 bin release info.txt


This is how the web page looks like:

![Screenshot 2024-11-04 194658](https://github.com/user-attachments/assets/7bb592bb-c210-440f-ad3e-b862299c11ee)

![Screenshot 2024-11-07 133906](https://github.com/user-attachments/assets/fd3215de-3966-4222-b302-595a540ca88b)




If you keep the "api:" block active, the device will be discovered by Home Assistant upon restart, appearing like this:

![Screenshot 2024-11-05 095728](https://github.com/user-attachments/assets/0c11c5cd-dbff-4e06-8870-e32d41f5d8e9)

![Screenshot 2024-11-05 095751](https://github.com/user-attachments/assets/42707df7-e443-449d-b715-8ab477cdaf76)



So you have entities to read all values and set online and offline (fallback) voltages, trigger "Full Fan Speed"; "Auto Fan Speed"; "Hibernate"; "Wake" using HA/NodeRed automations.

## MQTT Topics



### Sensors
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

- **Set Max Output Current**  
  - State Topic: `can-bus01/sensor/set_max_output_current/state`

- **Input Grid Voltage**  
  - State Topic: `can-bus01/sensor/input_grid_voltage/state`

- **Output Temperature**  
  - State Topic: `can-bus01/sensor/output_temperature/state`

- **Output Current**  
  - State Topic: `can-bus01/sensor/output_current/state`

### Numbers
- **CAN Voltage Set**  
  - State Topic: `can-bus01/number/can_voltage_set/state`

- **CAN Amp Set**  
  - State Topic: `can-bus01/number/can_amp_set/state`

- **Fallback Amp Set**  
  - State Topic: `can-bus01/number/fallback_amp_set/state`

- **Fallback Voltage Set**  
  - State Topic: `can-bus01/number/fallback_voltage_set/state`

### Buttons
- **CAN ON Button**  
  - State Topic: `can-bus01/button/can_on_button/state`  
  - Command Topic: `can-bus01/button/can_on_button/command`

- **CAN OFF Button**  
  - State Topic: `can-bus01/button/can_off_button/state`  
  - Command Topic: `can-bus01/button/can_off_button/command`

- **Fan Full Speed Button**  
  - State Topic: `can-bus01/button/fan_full_speed_button/state`  
  - Command Topic: `can-bus01/button/fan_full_speed_button/command`

- **Fan Auto Mode Button**  
  - State Topic: `can-bus01/button/fan_auto_mode_button/state`  
  - Command Topic: `can-bus01/button/fan_auto_mode_button/command`

So you can read all data and hibernate/wake; set fan to auto/full speed using MQTT. 


# Software references:

[Protocol_R4875g.xlsx](https://github.com/user-attachments/files/17571999/Protocol_R4875g.xlsx)

Adopted from: 

https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/post-1805865


# More References:


For more inspiration and lots of info and experimentation around these powerful "rectifiers".


(Useful if you have a related rectifier and want to adapt my code (the other varieties like the R4850G and R4850G2 have slightly different CAN message IDs and factors.)

https://www.beyondlogic.org/review-huawei-r4850g2-power-supply-53-5vdc-3kw/

https://endless-sphere.com/sphere/threads/rectifier-huawei-r4850g2-48v-42-58v-3000w.86038/

https://diysolarforum.com/threads/diy-chargenectifier.56329/

https://github.com/BotoX/huawei-r48xx-esp32

https://github.com/craigpeacock/Huawei_R4850G2_CAN 

https://github.com/haklein/r4850g2_arduino






