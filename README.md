# Hardware: 
![IMG_20241018_141436](https://github.com/user-attachments/assets/c75316e2-48f6-43c9-b544-35b82bb796bc)
![IMG_20241018_140546](https://github.com/user-attachments/assets/26adaf1d-337f-4390-b8ca-50e13a5c1673)
![IMG_20241018_140009](https://github.com/user-attachments/assets/7dc9d88e-ced0-493b-8f79-1956837783df)
![IMG_20241018_140743](https://github.com/user-attachments/assets/ea1e3ff2-27fd-47af-94fe-055ffd697be8)
![IMG_20241018_141733](https://github.com/user-attachments/assets/909a2692-d080-4f7c-9965-e06ca748c99c)
this is connecting 5v logic level from the CAN tranceiver module to the 3.3v ESP input pin.
the ESP32 I used managed to deal with the 5v input (but it's not designed for that!) and the tranceiver was fine receiving a 3.3v input logic from the ESP32, but you shouldn't rely on that. Add a logic level shifter beween the ESP32 and the tranceiver module.




