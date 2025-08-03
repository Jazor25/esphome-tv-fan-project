# ESPHome Fan Project
Control 2 Fans independently depending on the temperature of 2 distict DHT22 sensors.

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