# About this project

The goal of this DIY project is to create a WiFi-connected pedal to be used on stage to remotely control a mixing console via Bitfocus Companion.

## System Logic:
* **ESP32-C3 with an integrated 0.42" OLED display**: Flashed with Tasmota firmware and configured for our needs.
* **Onboard Microswitch (GPIO 9):** Cycles through mixer channels from `CH1` to `CH24` (rolling back to `1` after `24`). The selected channel is instantly updated and shown on the integrated 0.42" OLED screen.
* **External Foot Pedal (GPIO 2):** Sends live actions to Companion for the *currently selected channel*. Pressing the pedal sends an "Active" (e.g., MUTE) command, and releasing the pedal sends an "Inactive" (e.g., UNMUTE) command.

---

## Used Parts:

* **Development Board:** ESP32-C3 with an integrated 0.42" OLED display (Single-board solution, native USB JTAG/Serial debug unit, SSD1306 display driver - I2C interface, 72x40 resolution)
* **Actuator:** External Foot Pedal (Guitar pedal / Sewing machine pedal with a jack connector)
* **Jack connector:** 3.5mm female connector for the pedal
* **3D Printed Box:** custom-designed PLA/PET 3D-printed
* **USB-C Cable:** power supply for the ESP32

---

## Hardware Setup

The ESP32-C3 board features native USB functionality, allowing direct flashing and console logging without an external FTDI chip. The components are mapped to the following GPIO pins:

| Component | ESP32-C3 GPIO | Hardware State | Tasmota Component |
| :--- | :--- | :--- | :--- |
| **Integrated OLED SCL** | `GPIO 6` | Embedded I2C | `I2C SCL (1)` |
| **Integrated OLED SDA** | `GPIO 5` | Embedded I2C | `I2C SDA (1)` |
| **Onboard Status LED** | `GPIO 8` | Onboard LED | `Led_i 1` (Inverted) |
| **Onboard Button** | `GPIO 9` | Active LOW, internal pull-up | `Button 1` |
| **External Foot Pedal** | `GPIO 2` | Connected to GND, internal pull-up | `Switch 2` |

---

## Tasmota Config

### 1. Download and flash firmware
Get Tasmota32-C3 with LVGL support (`tasmota32c3-lvgl.factory.bin` for initial flash or `tasmota32c3-lvgl.bin` for upgrade) from https://github.com/tasmota/install/raw/firmware/firmware/unofficial/

### 2. Template Configuration
Go to **Configuration -> Other -> ESP32C3 Template** and paste the following JSON string to initialize the I2C display, onboard LED, micro-button (`Button1`), and external pedal (`Switch2`):

```json
{"NAME":"ESP32-C3 Stage Pedal","ARCH":"ESP32C3","GPIO":[1,1,161,1,1,640,608,1,320,32,1,0,6210,0,0,0,0,0,0,0,0,1],"FLAG":0,"BASE":1}
```

### 3. Tasmota Configuration
Go to **Tools -> Console** and paste the following strings. Change the first line according to your environment:

```
Backlog SSID1 my_ssid; Password1 my_pass; SSID2 my_other_ssid; Password2 my_pass;TimeZone 99;TimeDST 0,0,3,1,2,120;TimeSTD 0,0,10,1,3,60;Latitude 00.000000;Longitude 00.000000;Altitude 123;NtpServer1 192.168.1.1;NtpServer2 0;NtpServer3 0
Backlog SerialLog 2; DisplayModel 2; DisplayMode 0; SetOption20 1; SetOption13 1; ButtonTopic 0; SwitchMode2 1
OtaUrl https://github.com/tasmota/install/raw/refs/heads/firmware/firmware/unofficial/tasmota32c3-lvgl.bin
```
