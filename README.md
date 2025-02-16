# Home Assistant Presence Sensor Blueprint

## 📌 Overview
This blueprint automates lighting based on motion detection. It **saves the current state of all lights** in a room before turning them off and **restores the same lights** when motion is detected again.

🔹 **Main Features:**
- ✅ Works with **any motion sensor**
- ✅ **Saves the state of all lights** in the room
- ✅ **Restores the lights** when motion is detected
- ✅ Adjustable **delay before turning off** (seconds & minutes)
- ✅ **Easy setup** – No coding required!

---

## 🛠️ Requirements
To use this blueprint, you need:
- A **motion sensor** integrated with Home Assistant (e.g., Zigbee, Z-Wave, WiFi PIR sensors)
- Smart lights or light switches that Home Assistant can control

---

## 📥 Quick Import to Home Assistant

[![Open in Home Assistant](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fkimasf%2Fhomeassistant-blueprints%2Fmain%2Fpresence_sensor.yaml)

Alternatively, you can manually copy the YAML file to:
```bash
/config/blueprints/automation/YOUR_FOLDER/presence_sensor.yaml
```

---

## ⚙️ Configuration
### **1️⃣ Create a New Automation**
1. Go to **Settings** → **Automations & Scenes** → **Blueprints**.
2. Select **Presence Sensor Blueprint**.
3. Click **Create Automation**.

### **2️⃣ Customize Your Settings**
- **Motion Sensor** → Select your motion sensor.
- **Lights to Control** → Choose the lights in your room.
- **Turn-Off Delay** → Set the delay before lights turn off (in seconds/minutes).
- **Scene Name** → (Optional) Customize the saved scene name.

---

## 🔄 How It Works
1. When motion is **detected**, the blueprint **restores the previous lights** in the room.
2. When no motion is detected for the set delay, it **saves the current light state** and **turns them off**.
3. The next time motion is detected, the **same lights** will turn back on!

---

## 📝 YAML Code (For Manual Use)
```yaml
blueprint:
  name: "💡 Presence Sensor (Customizable)"
  description: "Turns off lights after a user-defined time of no motion and restores previous state."
  domain: automation
  input:
    motion_sensor:
      name: Motion Sensor
      description: "Select the motion sensor for detection."
      selector:
        entity:
          domain: binary_sensor

    lights:
      name: Lights
      description: "Select the lights to control."
      selector:
        entity:
          domain: light
          multiple: true

    scene_name:
      name: Scene Name
      description: "Enter a unique name for the scene (no spaces, use _ instead)."
      selector:
        text:

    delay_minutes:
      name: Delay (Minutes)
      description: "Minutes before turning off lights after no motion."
      default: 1
      selector:
        number:
          min: 0
          max: 60
          unit_of_measurement: "min"

    delay_seconds:
      name: Delay (Seconds)
      description: "Additional seconds before turning off lights."
      default: 0
      selector:
        number:
          min: 0
          max: 59
          unit_of_measurement: "sec"

variables:
  scene_full_name: !input scene_name

trigger:
  - platform: state
    entity_id: !input motion_sensor
    to: "on"
    id: Motion_ON

  - platform: state
    entity_id: !input motion_sensor
    to: "off"
    for:
      minutes: !input delay_minutes
      seconds: !input delay_seconds
    id: Motion_OFF

action:
  - choose:
      - conditions:
          - condition: trigger
            id: Motion_OFF
          - condition: state
            entity_id: !input motion_sensor
            state: "off"
        sequence:
          - service: scene.create
            data:
              snapshot_entities: !input lights
              scene_id: "{{ scene_full_name }}"

          - service: light.turn_off
            target:
              entity_id: !input lights

      - conditions:
          - condition: trigger
            id: Motion_ON
        sequence:
          - service: scene.turn_on
            data:
              entity_id: "scene.{{ scene_full_name }}"

          - service: scene.delete
            data:
              entity_id: "scene.{{ scene_full_name }}"

mode: single
```

---

## ❓ Support & Feedback
If you have any questions or suggestions, feel free to:
- Comment on the **GitHub Issues** page
- Join the **Home Assistant Community** discussions

🚀 Enjoy your smart home automation with **Presence Sensor Blueprint**! 🎉

