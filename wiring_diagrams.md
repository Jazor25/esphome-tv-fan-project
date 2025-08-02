# Wiring Diagrams - ESP Home Fan Project

## Pin Assignments Summary

### ESP32 GPIO Pin Usage
| GPIO Pin | Component | Description |
|----------|-----------|-------------|
| GPIO22   | DHT22 #1  | Temperature/Humidity Sensor 1 |
| GPIO21   | DHT22 #2  | Temperature/Humidity Sensor 2 |
| GPIO32   | FET Board #1 | Fan 1 Control Signal |
| GPIO25   | FET Board #2 | Fan 2 Control Signal |
| 3.3V     | DHT22 VCC | Power for both DHT22 sensors |
| GND      | DHT22 GND | Ground for both DHT22 sensors |

## Main Wiring Diagram

```
                           ┌─────────────────┐
                           │     ESP32       │
                           │   (ESP32 V4)    │
                           │                 │
┌─────────────┐           │  GPIO22  ────── │ ──── DHT22 #1 Data
│             │           │  GPIO21  ────── │ ──── DHT22 #2 Data
│  12V Power  │           │  GPIO32  ────── │ ──── FET Board #1 Signal
│   Supply    │           │  GPIO25  ────── │ ──── FET Board #2 Signal
│             │           │                 │
│   +12V ─────┼───────────│  VIN     ────── │ ──── 12V Input (via Buck Converter)
│    GND ─────┼───────────│  GND     ────── │ ──── Common Ground
└─────────────┘           │  3.3V    ────── │ ──── DHT22 VCC (both sensors)
                           └─────────────────┘
                                    │
                                    │
                           ┌─────────────────┐
                           │  LM2596s Buck   │
                           │   Converter     │
                           │   (24V→5V/3V)   │
                           │                 │
                           │  IN+  ←─ 12V    │
                           │  IN-  ←─ GND    │
                           │  OUT+ ──→ 5V    │ ──── To ESP32 VIN
                           │  OUT- ──→ GND   │ ──── Common Ground
                           └─────────────────┘
```

## DHT22 Sensors Wiring

```
DHT22 Sensor #1                    DHT22 Sensor #2
┌─────────────┐                    ┌─────────────┐
│      ┌─┐    │                    │      ┌─┐    │
│   1 ─┤ ├─ 4 │                    │   1 ─┤ ├─ 4 │
│   2 ─┤ ├─ 3 │                    │   2 ─┤ ├─ 3 │
│      └─┘    │                    │      └─┘    │
└─────────────┘                    └─────────────┘
Pin 1: VCC (3.3V)                  Pin 1: VCC (3.3V)
Pin 2: Data → GPIO22               Pin 2: Data → GPIO21
Pin 3: Not Connected               Pin 3: Not Connected
Pin 4: GND                         Pin 4: GND

Note: Add 10kΩ pull-up resistor between VCC and Data pin for each DHT22
```

## FET Switch Board Connections

```
FET Board #1 (400W)                FET Board #2 (400W)
┌─────────────────┐                ┌─────────────────┐
│  DC+ ←─ Fan 1 + │                │  DC+ ←─ Fan 2 + │
│  DC- ←─ Fan 1 - │                │  DC- ←─ Fan 2 - │
│                 │                │                 │
│  VCC ←─ 12V     │                │  VCC ←─ 12V     │
│  GND ←─ GND     │                │  GND ←─ GND     │
│  SIG ←─ GPIO32  │                │  SIG ←─ GPIO25  │
└─────────────────┘                └─────────────────┘
       │                                  │
       │                                  │
┌─────────────┐                    ┌─────────────┐
│   Fan #1    │                    │   Fan #2    │
│  (12V RPM)  │                    │  (12V RPM)  │
│             │                    │             │
│    + Red    │                    │    + Red    │
│    - Black  │                    │    - Black  │
└─────────────┘                    └─────────────┘
```

## Power Distribution

```
┌─────────────┐
│  12V 1-2A   │
│ Power Supply│
│             │
│    +12V ────┼─┬─── LM2596s Input
│     GND ────┼─┼─── FET Board #1 VCC
└─────────────┘ │    FET Board #2 VCC
                │    Fan #1 Power (via FET)
                │    Fan #2 Power (via FET)
                │
                └─── Common Ground (All Components)

LM2596s Output:
5V ────── ESP32 VIN
GND ───── Common Ground
```

## Complete Connection Table

| Component | Pin/Terminal | Connects To | Wire Color Suggestion |
|-----------|--------------|-------------|----------------------|
| **ESP32** | | | |
| | VIN | LM2596s OUT+ | Red |
| | GND | Common Ground | Black |
| | 3.3V | DHT22 #1 & #2 VCC | Red |
| | GPIO22 | DHT22 #1 Data | Yellow |
| | GPIO21 | DHT22 #2 Data | Orange |
| | GPIO32 | FET Board #1 SIG | Green |
| | GPIO25 | FET Board #2 SIG | Blue |
| **DHT22 #1** | | | |
| | VCC | ESP32 3.3V | Red |
| | Data | ESP32 GPIO22 | Yellow |
| | GND | Common Ground | Black |
| **DHT22 #2** | | | |
| | VCC | ESP32 3.3V | Red |
| | Data | ESP32 GPIO21 | Orange |
| | GND | Common Ground | Black |
| **FET Board #1** | | | |
| | VCC | 12V Supply + | Red |
| | GND | Common Ground | Black |
| | SIG | ESP32 GPIO32 | Green |
| | DC+ | Fan #1 + | Red |
| | DC- | Fan #1 - | Black |
| **FET Board #2** | | | |
| | VCC | 12V Supply + | Red |
| | GND | Common Ground | Black |
| | SIG | ESP32 GPIO25 | Blue |
| | DC+ | Fan #2 + | Red |
| | DC- | Fan #2 - | Black |
| **LM2596s** | | | |
| | IN+ | 12V Supply + | Red |
| | IN- | Common Ground | Black |
| | OUT+ | ESP32 VIN | Red |
| | OUT- | Common Ground | Black |

## Safety Notes

1. **Power Supply Ratings**: Ensure 12V supply can handle total current draw (ESP32 + 2 fans)
2. **Heat Dissipation**: FET boards may generate heat under load - ensure adequate ventilation
3. **Pull-up Resistors**: DHT22 sensors require 10kΩ pull-up resistors on data lines
4. **Voltage Regulation**: Set LM2596s output to 5V for ESP32 VIN pin
5. **Common Ground**: All components must share common ground connection
6. **Wire Gauge**: Use appropriate wire gauge for 12V fan current (typically 18-20 AWG)

## Testing Procedure

1. Power on system without fans connected
2. Verify ESP32 boots and connects to WiFi
3. Check DHT22 sensor readings in Home Assistant
4. Test FET board switching with manual override
5. Connect fans and test temperature-based control
6. Monitor for proper on/off hysteresis behavior

## Troubleshooting

| Issue | Check |
|-------|-------|
| DHT22 not reading | Pull-up resistor, wiring, power |
| Fan not switching | FET board power, signal connection |
| ESP32 not booting | Power supply voltage, current capacity |
| Erratic behavior | Common ground, power supply stability |
