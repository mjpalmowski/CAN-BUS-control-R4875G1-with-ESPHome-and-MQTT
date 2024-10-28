# Hardware: 
![IMG_20241018_141436](https://github.com/user-attachments/assets/c75316e2-48f6-43c9-b544-35b82bb796bc)
ESP32 board with TJA1050 CAN-BUS tranceiver board. (needs level shifter, but this worked)

![IMG_20241018_140009](https://github.com/user-attachments/assets/aedbd152-b3ed-4c68-9e8d-fad47455ab69)
This is the Huawai R4875G1 power module that we are trying to control
it has an adapter specific for the R4875G1 and G5 series, can be found on AliE.
![IMG_20241018_140546](https://github.com/user-attachments/assets/26adaf1d-337f-4390-b8ca-50e13a5c1673)

![IMG_20241018_140743](https://github.com/user-attachments/assets/ea1e3ff2-27fd-47af-94fe-055ffd697be8)
TJA1050 CAN-BUS tranceiver board


![IMG_20241018_141733](https://github.com/user-attachments/assets/909a2692-d080-4f7c-9965-e06ca748c99c)
this is connecting 5v logic level from the CAN tranceiver module to the 3.3v ESP input pin.
the ESP32 I used managed to deal with the 5v input (but it's not designed for that!) and the tranceiver was fine receiving a 3.3v input logic from the ESP32, but you shouldn't rely on that. Add a logic level shifter beween the ESP32 and the tranceiver module.

if you don't want to spend money on the adapter board you can get the module to turn on by shorting these pads to DC-minus: 
![Screenshot 2024-10-18 164741](https://github.com/user-attachments/assets/edd97e21-da8d-49c3-851b-2305f1d71256)
![Screenshot 2024-10-18 165143](https://github.com/user-attachments/assets/8a0f7d83-c754-46e7-8dd0-eef3c1ae49cb)

the free opposing pair of contacts (of the three contacts in the centre) are your CAN-BUS. CAN-High is on the bottom and CAN-High is at the top, CAN-L is on the other side at the same position.
![can](https://github.com/user-attachments/assets/abf646de-7ed0-40bd-977f-927654330967)

#Software:

You can use Homeassistant's ESPHome plugin to add a new ESPHome device, add the code in the attached YAML file 
"CAN-R4875G1-ESP32.YAML"
(just copy and paste all the code that's underneath "captive_portal:" into your new ESPHome device YAML.  
If you don't need MQTT comment out the "mqtt:" code block
If you don't need Homeassistant comment out the "api:" code block 
it will be discovered by Home assistant upon restart, it looks like this: 
![Screenshot 2024-10-18 170221](https://github.com/user-attachments/assets/00b6da9a-1fe3-4be9-9083-7ba2df3a7ec5)
![Screenshot 2024-10-18 170149](https://github.com/user-attachments/assets/c8e686f8-5a49-41f0-8d6d-133be1017357)

MQTT topics look like this:

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'AC Power In':

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/ac_power_in/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'DC Power Out':

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/dc_power_out/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'Grid Frequency':

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/grid_frequency/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'Input Current':

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/input_current/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'Output Voltage':

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/output_voltage/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'Set Max Output Current':

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/set_max_output_current/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'Input Grid Voltage
[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/input_grid_voltage/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'Output Temperature'

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/output_temperature/state'

[07:19:49][C][mqtt.sensor:028]: MQTT Sensor 'Output Current':

[07:19:49][C][mqtt.sensor:032]:   State Topic: 'can-bus01/sensor/output_current/state'

[07:19:49][C][mqtt.number:033]: MQTT Number 'CAN Voltage Set':

[07:19:49][C][mqtt.number:034]:   State Topic: 'can-bus01/number/can_voltage_set/state'

[07:19:49][C][mqtt.number:033]: MQTT Number 'CAN Amp Set':

[07:19:49][C][mqtt.number:034]:   State Topic: 'can-bus01/number/can_amp_set/state'

[07:19:49][C][mqtt.number:033]: MQTT Number 'Fallback Amp Set':

[07:19:49][C][mqtt.number:034]:   State Topic: 'can-bus01/number/fallback_amp_set/state'

[07:19:49][C][mqtt.number:033]: MQTT Number 'Fallback Voltage Set':

[07:19:49][C][mqtt.number:034]:   State Topic: 'can-bus01/number/fallback_voltage_set/state'

[07:19:49][C][mqtt.button:029]: MQTT Button 'CAN ON Button': 

[07:19:49][C][mqtt.button:030]:   State Topic: 'can-bus01/button/can_on_button/state'

[07:19:49][C][mqtt.button:030]:   Command Topic: 'can-bus01/button/can_on_button/command'

[07:19:49][C][mqtt.button:029]: MQTT Button 'CAN OFF Button': 

[07:19:49][C][mqtt.button:030]:   State Topic: 'can-bus01/button/can_off_button/state'

[07:19:49][C][mqtt.button:030]:   Command Topic: 'can-bus01/button/can_off_button/command'

[07:19:49][C][mqtt.button:029]: MQTT Button 'Fan Full Speed Button': 

[07:19:49][C][mqtt.button:030]:   State Topic: 'can-bus01/button/fan_full_speed_button/state'

[07:19:49][C][mqtt.button:030]:   Command Topic: 'can-bus01/button/fan_full_speed_button/command'

[07:19:49][C][mqtt.button:029]: MQTT Button 'Fan Auto Mode Button': 

[07:19:49][C][mqtt.button:030]:   State Topic: 'can-bus01/button/fan_auto_mode_button/state'

[07:19:49][C][mqtt.button:030]:   Command Topic: 'can-bus01/button/fan_auto_mode_button/command'




