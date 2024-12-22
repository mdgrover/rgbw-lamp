# rgbw-lamp
3D printed design with ESP32C6 and RGBW Neopixels

**Based on this model by BambuLab:** \
https://makerworld.com/en/models/13717
- Base component re-engineered to support an ESP32 wireless-capable board and RGBW neopixel ring
- Lamps printed in BambuLab Jade White PLA
- Base printed in BambuLab Black PLA

  <img width="331" alt="image" src="https://github.com/user-attachments/assets/969dc14e-5b45-4c4f-81a7-e4d51cc9c33b" />

**Seeed Studio XIAO EPS32C6 board** \
https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/
- Two 32-bit RISC-V processors
- Complete 2.4GHz Wi-Fi subsystem
- BLE: Bluetooth 5.0, Bluetooth Mesh
- Zigbee
- Thread

<img width="456" alt="image" src="https://github.com/user-attachments/assets/0b4a1ac6-2231-41d8-8cbc-cb6cc51f5c0d" />


**NeoPixel Ring - 12 x 5050 RGBW LEDs w/ Integrated Drivers - Natural White - ~4500K** \
https://www.adafruit.com/product/2852
- 37mm ring with 12 individually addressable RGBW LEDs
- 8-bit PWM per channel (8 x 4 channels = 32-bit color overall)
- The PWM is built into each LED-chip - set the color and it continues to PWM all the LEDs
- Single microcontroller pin controls all 12 neopixels and more can be chained on
- Each LED has ~18mA constant current drive
- 5v power supply

<img width="522" alt="image" src="https://github.com/user-attachments/assets/b584e083-c650-443c-8b42-5321cfd0fce9" />

\
Applications included in this project as Arduino sketches:
- **Xiao_C6_basic_lamp.ino** - Simple code shipped with the lamp
- **Xiao_C6_RGBW_strandtest.ino** - Demonstrates more RGBW features of the NeoPixel ring
- **Xiao_C6_wifi_RGBW_dual_core_api.ino** - Connects to WiFi and provides and API for controlling the lamp
- **Xiao_C6_ap_RGBW_dual_core_api.ino** - Creates a WiFi AP and provides and API for controlling the lamp
