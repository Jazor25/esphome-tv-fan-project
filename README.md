# ESPHome TV Fan Project

⚠️ **Security Notice**: Please read [SECURITY.md](SECURITY.md) and create your own `secrets.yaml` before use!

This project describes how to build a thermostat-controlled fan control system for cooling your Playstation, Tuner or other hardware in a cabinet. I also built a 3D printed case which fits all components perfectly.

This setup controls 2 fans independently, based on the temperature of 2 distinct DHT22 sensors, but could easily be extended to control more fans and/or DHT22's. 

This solution can natively be integrated into Home Assistant using ESPHome, but it also runs autonomously and controls fans based on the temperature in the cabinet without it.

# Motivation
I wanted to build a TV/Media setup in our living room that is as clean as possible. This means (mostly) no devices visible on or in the TV board. Hardware running in closed cabinets without cooling results in overheated hardware, so I needed to find a solution. I already built something similar based on an Arduino Uno 3 years ago, which did no longer work properly and had no integration with Home Assistant.

This project is inspired by the excellent work of [patrickcollins12 on GitHub](https://github.com/patrickcollins12/esphome-fan-controller). I built my own project for several reasons:
* I only had 3 PIN fans from an older similar setup, no PWM conrol
* I wanted to have a Transistor controled switch solution, to have a reliant way to switch of both fans (not always reliantly possible using PWM signals only)
* My Playstation 5 becomes hot as hell pretty fast, so the fan needs to run on 100% anyways. Also Noctua fans are so silent, that decreasing fan speed is not really necessary from my point of view. 

## Visuals
(More images are in the /docs folder)

![Installed Case (Closed)](img/installed_case_closed.jpg)
![Mounted Case (Open)](img/fully_mounted_case_open.jpg)
![cabinet](img/cabinet.jpg)
![Installed Case in cabinet](img/box_installed_in_cabinet.jpg)
![Installed Fan 1](img/fan_installed_1.jpg)
![Installed Fan 2](img/fan_installed_2.jpg)
![Home Assistant Integration](img/home_assistant_integration.png)

## Hardware
* ESP-32 (Used here: ESP32 V4 with CP2102, AZDelivery)
* 2x DC 5V-36V 400W FET Trigger Switch Board
* 1x LM2596s voltage regulator 24V 12V 5V 3V (Buck Transformer)
* 2x DHT22
* 2x RPM Fan (2 PINs used)
* 12V 1-2A Power Supply
* 12V Male to Female Adapter

## Wiring & Pin Assignments

### GPIO Pin Usage
| GPIO Pin | Component | Description |
|----------|-----------|-------------|
| GPIO22   | DHT22 #1  | Temperature/Humidity Sensor 1 |
| GPIO21   | DHT22 #2  | Temperature/Humidity Sensor 2 |
| GPIO32   | FET Board #1 | Fan 1 Control Signal |
| GPIO25   | FET Board #2 | Fan 2 Control Signal |

### Wiring Diagrams
- **[Complete Wiring Guide](docs/wiring_diagrams.md)** - Detailed connection tables, safety notes, and troubleshooting
- **[Visual Schematic](docs/detailed_schematic.txt)** - ASCII art diagram showing component connections
- **[Cable Assignments](img/cable_assignments.png)** - Physical cable reference

### Key Connections
- **Power**: 12V supply → LM2596s (set to 3.3V) → ESP32 VIN
- **Sensors**: DHT22 data pins to GPIO22/21, powered by ESP32 3.3V
- **Fan Control**: GPIO32/25 → FET board signal pins
- **Ground**: Common ground for all components

## Features
- **Independent Control**: Each fan controlled by its own temperature sensor
- **Configurable Thresholds**: Set custom ON/OFF temperatures via Home Assistant
- **Hysteresis Control**: Prevents rapid fan switching
- **Web Interface**: Built-in web server for monitoring and control
- **Home Assistant Integration**: Full API integration with encryption

## 3D Case
* 5x Ruthex Thread Insert M3
* 5x M3 Screw
* 7x Ruthex Thread Insert M2
* 7x M2 Screw

## Some assembly hints
* The Buck Transformer needs to be adapted to 3.3V precisely to not destroy the ESP32 and other attached modules. Therefore, put 12V + and - respectively on the IN Pins, connect a multimeter (set to Voltage) to the OUT Pins and turn the screw of the poti with a screwdriver until the Multimeter shows 3.3V.
* The 3D case is designed to be as small as possible. Therefore, there is not too much flexibility in how to mount things together. In case you solder PINs on each component (where necessary), make sure that one of the two transistor boards has the PINs soldered "upside down" - Otherwise the box won't close.
In case of doubt, take a look at the images in the img folder.
* Be careful that open ends of 12V and 3.3V cables do not touch each other. This may cause things to break quite quickly. Use cable shrink tubes or electrical tapes to avoid this, where jumper cables (if used) are at risk to touch each other to cause short circuits. 

## License
This project is published under [CC BY-NC-SA
4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).