# Adding ESPHome Fan Project to Home Assistant

## Method 1: ESPHome Add-on (Recommended)

### Prerequisites
- Home Assistant with Supervisor (Home Assistant OS or Supervised)
- ESPHome Add-on installed

### Step-by-Step Instructions

#### 1. Install ESPHome Add-on
1. Go to **Settings** → **Add-ons** → **Add-on Store**
2. Search for "ESPHome" 
3. Click **Install** on the official ESPHome add-on
4. Start the add-on and enable **Start on boot**

#### 2. Add Your Device
1. Open ESPHome add-on web UI
2. Click **+ NEW DEVICE**
3. Choose **Continue** 
4. Enter device details:
   - **Name**: `tv-fan-project`
   - **Device Type**: Choose ESP32
5. Skip the wireless credentials (we'll use existing config)

#### 3. Upload Your Configuration
1. In ESPHome dashboard, click **EDIT** on your new device
2. Replace the generated content with your `tv-fan-project.yaml` content
3. Create a `secrets.yaml` file with:
   ```yaml
   wifi_ssid: "Your_WiFi_Name"
   wifi_password: "Your_WiFi_Password"
   ```
4. Click **SAVE**

#### 4. Compile and Flash
1. Click **INSTALL** 
2. Choose **Wirelessly** (if device is already flashed) or **Plug into this computer**
3. Wait for compilation and upload

#### 5. Auto-Discovery in Home Assistant
- Once online, the device should appear in **Settings** → **Devices & Services**
- Look for "ESPHome" integration with your device
- Click **CONFIGURE** if prompted

## Method 2: Standalone ESPHome Installation

### If you're running ESPHome separately (not as HA add-on):

#### 1. Ensure API Configuration
Your configuration already has the API enabled:
```yaml
api:
  encryption:
    key: "YOUR_API_ENCRYPTION_KEY_HERE"
```

#### 2. Manual Integration
1. Go to **Settings** → **Devices & Services**
2. Click **+ ADD INTEGRATION**
3. Search for "ESPHome"
4. Enter your ESP32's IP address
5. Enter the encryption key from your ESPHome config

## Method 3: MQTT Integration (Alternative)

### If you prefer MQTT, add this to your ESPHome config:

```yaml
mqtt:
  broker: your_mqtt_broker_ip
  username: !secret mqtt_username
  password: !secret mqtt_password
  discovery: true
  discovery_retain: false
  topic_prefix: esphome/tv_fan_project
```

## Device Configuration for Better HA Integration

### Add Device Information
Add this to your ESPHome configuration for better device identification:

```yaml
esphome:
  name: tv-fan-project
  friendly_name: TV Fan Project
  comment: "Dual fan temperature control system"
  project:
    name: "custom.tv_fan_control"
    version: "1.0"

# Device information for Home Assistant
device_info:
  manufacturer: "Custom Build"
  model: "ESP32 Fan Controller"
  sw_version: "1.0"
```

## Expected Home Assistant Entities

After successful integration, you'll see these entities:

### 🌡️ **Climate Sensors**
- `sensor.tv_fan_project_temperature_1`
- `sensor.tv_fan_project_temperature_2` 
- `sensor.tv_fan_project_humidity_1`
- `sensor.tv_fan_project_humidity_2`

### 🔌 **Switches & Controls**
- `switch.tv_fan_project_fan_1_switch`
- `switch.tv_fan_project_fan_2_switch`
- `number.tv_fan_project_fan_on_temperature`
- `number.tv_fan_project_fan_on_temperature_2`
- `number.tv_fan_project_fan_off_temperature_1`
- `number.tv_fan_project_fan_off_temperature`

### 📊 **System Monitoring**
- `sensor.tv_fan_project_uptime`
- `sensor.tv_fan_project_wifi_signal`
- `text_sensor.tv_fan_project_fan_control_status`

### 🏠 **Device Status**
- `binary_sensor.tv_fan_project_status` (online/offline)

## Creating a Dashboard Card

### Example Lovelace Card Configuration:

```yaml
type: entities
title: TV Fan Control System
entities:
  - entity: text_sensor.tv_fan_project_fan_control_status
    name: System Status
  - type: divider
  - entity: sensor.tv_fan_project_temperature_1
    name: Temperature Zone 1
  - entity: sensor.tv_fan_project_temperature_2
    name: Temperature Zone 2
  - type: divider
  - entity: switch.tv_fan_project_fan_1_switch
    name: Fan 1 Manual Control
  - entity: switch.tv_fan_project_fan_2_switch
    name: Fan 2 Manual Control
  - type: divider
  - entity: number.tv_fan_project_fan_on_temperature
    name: Fan 1 ON Temp
  - entity: number.tv_fan_project_fan_off_temperature_1
    name: Fan 1 OFF Temp
  - entity: number.tv_fan_project_fan_on_temperature_2
    name: Fan 2 ON Temp
  - entity: number.tv_fan_project_fan_off_temperature
    name: Fan 2 OFF Temp
```

## Automation Example

### Create automations in Home Assistant:

```yaml
# Example: Notification when fan turns on
automation:
  - alias: "Fan System Alert"
    trigger:
      - platform: state
        entity_id: switch.tv_fan_project_fan_1_switch
        to: 'on'
      - platform: state
        entity_id: switch.tv_fan_project_fan_2_switch
        to: 'on'
    action:
      - service: notify.mobile_app_your_phone
        data:
          message: "Fan {{ trigger.entity_id.split('.')[1] }} activated due to high temperature"
```

## Troubleshooting

### Device Not Appearing
1. **Check Network**: Ensure ESP32 and HA are on same network
2. **Verify API**: Confirm API encryption key matches
3. **Check Logs**: Look in HA logs for connection errors
4. **Manual Add**: Try adding via IP address manually

### Connection Issues
1. **Firewall**: Ensure port 6053 is open for ESPHome API
2. **WiFi Signal**: Check `sensor.tv_fan_project_wifi_signal` strength
3. **Restart**: Reboot both ESP32 and Home Assistant

### Entity Names
- Entity names will be prefixed with your device name
- Use **Settings** → **Devices & Services** → **ESPHome** → your device to customize entity names

## Advanced Features

### Group Configuration
Create groups for easier management:

```yaml
# In configuration.yaml
group:
  fan_control_system:
    name: "TV Fan Control System"
    entities:
      - switch.tv_fan_project_fan_1_switch
      - switch.tv_fan_project_fan_2_switch
      - sensor.tv_fan_project_temperature_1
      - sensor.tv_fan_project_temperature_2
```

### Template Sensors
Create calculated sensors:

```yaml
# Average temperature sensor
template:
  - sensor:
      - name: "Average Room Temperature"
        unit_of_measurement: "°C"
        state: >
          {{ ((states('sensor.tv_fan_project_temperature_1') | float + 
               states('sensor.tv_fan_project_temperature_2') | float) / 2) | round(1) }}
```

Once you follow these steps, your ESPHome fan control system will be fully integrated into Home Assistant with all entities available for monitoring, control, and automation!
