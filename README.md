# Decco Home Assistant Blueprints

**Note: You MUST create at least one Sonos alarm in the Sonos app before using these blueprints.**

A collection of powerful Home Assistant automation blueprints for smart home control, including calendar-based lighting, device controllers, and alarm management.

## 📋 Overview

This repository contains six automation blueprints designed to enhance your Home Assistant setup:

1. **Calendar Event Light Controller** - Dynamic light control based on calendar events
2. **Aqara Magic Cube Controllers** (3 variants) - Multi-function cube controllers for lights and media
3. **Sonos Alarm** - Calendar-based wake-up alarm system
4. **IKEA SYMFONISK Controller** - Rotary remote control automation

---

## 🚀 Installation

### Method 1: Import via URL

1. In Home Assistant, go to **Configuration** → **Blueprints**
2. Click the **Import Blueprint** button
3. Enter the URL of the desired blueprint from this repository
4. Click **Preview** and then **Import**

### Method 2: Manual Installation

1. Copy the desired `.yaml` file from this repository
2. Place it in your Home Assistant `config/blueprints/automation/` directory
3. Restart Home Assistant or reload automations

---

## 📘 Blueprints Documentation

### 1. Calendar Event Light Controller

**File:** `calendar_event_light.yaml`

#### Description

Automatically controls a light's color and brightness based on calendar events. When the light turns on, it checks for matching calendar events and applies corresponding colors and brightness levels.

#### Inputs

- **Calendar Entity** - Calendar to monitor for events
- **Light Entity** - Target light to control
- **Offset Minutes** - Time offset to event (positive = after, negative = before)
  - Range: -1440 to 1440 minutes
  - Default: 0
- **No Event Brightness** - Default brightness when no event matches (1-100%)
  - Default: 50%
- **No Event Color** - Default RGB color when no event matches
  - Default: White [255, 255, 255]
- **Event Rules** - List of matching rules with:
  - Text to match in event summary
  - RGB color to apply
  - Brightness percentage (1-100%)

#### Use Cases

- Color-code your lighting based on calendar appointments
- Set different lighting moods for meetings, focus time, or personal events
- Automatically adjust lighting before events start

#### Example Configuration

```yaml
Event Rules:
  - text: "meeting"
    color: [0, 100, 255]  # Blue
    brightness: 75
  - text: "focus time"
    color: [255, 200, 0]  # Warm yellow
    brightness: 60
```

---

### 2. Aqara Magic Cube Controllers

Three variants are available for different integration methods:

#### 2a. Magic Cube - deCONZ Integration

**File:** `magic-cube-deconz.yaml`

#### 2b. Magic Cube - Zigbee2MQTT Integration

**File:** `magic-cube-z2m.yaml`

#### 2c. Magic Cube - ZHA Integration

**File:** `magic-cube-zha.yaml`

#### Description

Control lights and media players using an Aqara Magic Cube (MFKZQ01LM). Each side of the cube can control a different entity with multiple gesture-based actions.

#### Supported Gestures

| Gesture | Light Action | Media Player Action |
|---------|-------------|--------------------|
| Rotate 90° / 180° | Select cube side | Select cube side |
| Slide | Toggle color temperature | Next track |
| Shake | Flash light | Previous track |
| Rotate Left/Right | Decrease/Increase brightness | Decrease/Increase volume |
| Double Tap/Knock | Toggle on/off | Play/Pause |
| Fall | (Not assigned) | (Not assigned) |

#### Inputs

- **Magic Cube** - The cube device (integration-specific selector)
- **Cube Side Input** - Input number helper to store current side (0-5 or 1-6 depending on integration)
- **Color Temperature Settings:**
  - Cold (153-500 mireds) - Default: 350
  - Warm (153-500 mireds) - Default: 150
  - Warmer (153-500 mireds) - Default: 75
- **Rotation Sensitivity** - Percentage (1-100%) - Default: 50%
- **Side 1-6 Entities** - Light or media player for each cube side

#### Prerequisites

- **Create an Input Number helper:**
  - Go to Settings → Devices & Services → Helpers
  - Click Create Helper → Number
  - Min: 0 (or 1 for ZHA), Max: 5 (or 6 for ZHA), Step: 1
  - Name it (e.g., "Magic Cube Side")

#### Use Cases

- Control bedroom lights on one side, living room on another
- Manage multiple media players by flipping the cube
- Adjust color temperature by sliding
- Quick volume/brightness control by rotating

---

### 3. Wake Up Alarm for Calendar Event

**File:** `sonos_alarm.yaml`

#### Description

Automatically sets a Sonos alarm based on the first calendar event in a specified time range. Perfect for dynamic wake-up times that adjust to your schedule.

#### Inputs

- **Media Player** - Sonos media player entity
- **Schedule Time** - Daily time to check calendar and set alarm
- **Calendar** - Calendar entity to monitor
- **Sonos Alarm Switch** - The Sonos alarm switch entity to update
- **Input Datetime** - Input datetime helper to store the alarm time
- **Offset Timedelta** - Time before event to trigger alarm
  - Format: HH:MM:SS
  - Default: "02:00:00" (2 hours before)
- **Light** - Light entity to turn on gradually at alarm time
- **Person** - Person entity to track home/away status

#### Features

- Searches for calendar events between 8 AM and 12 PM
- Sets alarm 2 hours before the first event (configurable)
- Automatically enables alarm when person is home
- Disables alarm when person leaves
- Gradual light turn-on (3-minute transition) when alarm triggers
- Updates alarm time dynamically based on calendar changes

#### Prerequisites

- Create an Input Datetime helper for alarm time
- Configure a Sonos alarm via the Sonos app first
- The alarm switch entity will be created automatically by Home Assistant

#### Use Cases

- Wake up 2 hours before your first meeting
- Automatic alarm adjustment for variable schedules
- Disable alarms automatically when traveling
- Gradual wake-up lighting

---

### 4. IKEA E1744 SYMFONISK Rotary Remote Controller

**File:** `symfonisk.yaml`

#### Description

Comprehensive controller automation for the IKEA SYMFONISK sound controller. Supports deCONZ, ZHA, and Zigbee2MQTT integrations with customizable actions and looping support.

#### Inputs

- **Integration** - Select deCONZ, ZHA, or Zigbee2MQTT
- **Controller Device** - Device entity (for deCONZ/ZHA)
- **Controller Entity** - Sensor entity (for Zigbee2MQTT)
- **Helper - Last Controller Event** - Input text helper (required)
- **Debounce Delay** - Delay in milliseconds (0-1000ms)
  - Default: 0 (disabled)
  - Recommended: 100ms if experiencing duplicate events

#### Actions

- **Rotate Left** - Custom action when rotating counterclockwise
- **Rotate Left Stop** - Action when stopping rotation
- **Rotate Right** - Custom action when rotating clockwise
- **Rotate Right Stop** - Action when stopping rotation
- **Remote Short Press** - Single button press action
- **Remote Double Press** - Double button press action
- **Remote Triple Press** - Triple button press action

#### Advanced Options

- **Rotate Left/Right Loop** - Continuously run action while rotating
- **Maximum Loop Repeats** - Safety limit (1-5000 repeats)
  - Default: 500

#### Prerequisites

- **Create an Input Text helper:**
  - Settings → Devices & Services → Helpers
  - Create Helper → Text
  - Max length: 255 characters
  - Name it (e.g., "SYMFONISK Last Event")

#### Use Cases

- Control light brightness by rotation
- Adjust media player volume
- Switch scenes with button presses
- Create complex multi-action sequences
- Connect to Hooks for pre-built integrations

#### Additional Resources

- Part of [Awesome HA Blueprints](https://epmatt.github.io/awesome-ha-blueprints/) project
- [Full Documentation](https://epmatt.github.io/awesome-ha-blueprints/docs/blueprints/controllers/ikea_e1744)
- [Available Hooks](https://epmatt.github.io/awesome-ha-blueprints/docs/blueprints/controllers/ikea_e1744#available-hooks)

---

## 🛠️ Configuration Tips

### Creating Input Helpers

Many blueprints require input helpers. To create them:

1. Go to Settings → Devices & Services → Helpers
2. Click Create Helper
3. Choose the appropriate type:
   - Number - For cube side storage
   - Datetime - For alarm times
   - Text - For storing controller events
4. Configure the helper according to blueprint requirements
5. Use the helper entity ID in your automation

### Common Issues

#### Magic Cube Not Responding

- Verify the cube is properly paired with your integration
- Check that the input number helper is created correctly
- Ensure entities are not unavailable
- Check Home Assistant logs for errors

#### Calendar Events Not Matching

- Event matching is case-insensitive
- Match text should be contained in the event summary
- Check calendar entity is returning events correctly
- Verify offset minutes are set appropriately

#### SYMFONISK Multiple Triggers

- Increase the debounce delay to 100ms
- Ensure only one automation uses the same device
- Check that the input text helper is unique

---

## 📝 Blueprint Comparison

| Blueprint | Difficulty | Integrations | Requires Helpers |
|-----------|-----------|--------------|------------------|
| Calendar Event Light | Easy | Any | No |
| Magic Cube (deCONZ) | Medium | deCONZ | Yes (Number) |
| Magic Cube (Z2M) | Medium | Zigbee2MQTT | Yes (Number) |
| Magic Cube (ZHA) | Medium | ZHA | Yes (Number) |
| Sonos Alarm | Advanced | Sonos | Yes (Datetime) |
| SYMFONISK | Medium | deCONZ/ZHA/Z2M | Yes (Text) |

---

## 🤝 Contributing

Feel free to submit issues or pull requests to improve these blueprints. When contributing:

1. Test your changes thoroughly
2. Update documentation for any new features
3. Follow YAML formatting conventions
4. Include example configurations

---

## 📄 License

These blueprints are provided as-is for use in Home Assistant. The SYMFONISK blueprint is part of the Awesome HA Blueprints project by EPMatt.

---

## 🔗 Useful Links

- [Home Assistant Documentation](https://www.home-assistant.io/docs/)
- [Blueprint Documentation](https://www.home-assistant.io/docs/automation/using_blueprints/)
- [Awesome HA Blueprints](https://epmatt.github.io/awesome-ha-blueprints/)
- [Home Assistant Community Forum](https://community.home-assistant.io/)

---

## ⚠️ Disclaimer

These blueprints are community-created and not officially supported by Home Assistant or device manufacturers. Always test automations in a safe environment before deploying to production systems.

**Last Updated:** October 2025  
**Repository:** git.decco.net/git/Hass
