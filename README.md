# 🏠 Home Assistant Presence Sensor Blueprint  

## 📌 Overview  
This blueprint automates lighting based on motion detection. It **saves the current state of all lights** in a room before turning them off and **restores the same lights** when motion is detected again.  

### 🔹 Main Features:  
- ✅ Works with **any motion sensor**  
- ✅ **Saves the state of all lights** in the room  
- ✅ **Restores the lights** when motion is detected  
- ✅ Adjustable **delay before turning off** (seconds & minutes)  
- ✅ **Easy setup** – No coding required!  

---  

## 📥 Quick Import to Home Assistant  

Click the button below to **import this blueprint** directly into Home Assistant:  

[![Open your Home Assistant instance and import this Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?repository_url=https://raw.githubusercontent.com/kimasf/homeassistant-blueprints/main/presence_sensor.yaml)  

Alternatively, you can manually copy the YAML file to:  
```bash
/config/blueprints/automation/kimasf/presence_sensor.yaml
```

---

## ⚙️ Installation & Configuration  

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
  name: Presence Sensor Blueprint
  description: Automates lighting based on motion, saves & restores light state.
  domain: automation
  input:
    motion_sensor:
      name: Motion Sensor
      description: Select the motion sensor.
      selector:
        entity:
          domain: binary_sensor
          device_class: motion
    lights:
      name: Lights to Control
      description: Choose lights to turn on/off.
      selector:
        entity:
          domain: light
          multiple: true
    delay_minutes:
      name: Turn-Off Delay (Minutes)
      description: Set the delay before turning lights off.
      default: 1
      selector:
        number:
          min: 0
          max: 60
          unit_of_measurement: minutes
    delay_seconds:
      name: Turn-Off Delay (Seconds)
      description: Additional delay in seconds.
      default: 0
      selector:
        number:
          min: 0
          max: 59
          unit_of_measurement: seconds
    scene_name:
      name: Scene Name
      description: Customize the saved scene name.
      default: "presence_lights"
      selector:
        text:

variables:
  scene_full_name: !input scene_name

trigger:
  - platform: state
    entity_id: !input motion_sensor
    to: "on"
    id: motion_on
  - platform: state
    entity_id: !input motion_sensor
    to: "off"
    for:
      minutes: !input delay_minutes
      seconds: !input delay_seconds
    id: motion_off

condition: []

action:
  - choose:
      - conditions:
          - condition: trigger
            id: motion_off
        sequence:
          - service: scene.create
            data:
              scene_id: "{{ scene_full_name }}"
              snapshot_entities: !input lights
          - service: light.turn_off
            target:
              entity_id: !input lights
      - conditions:
          - condition: trigger
            id: motion_on
        sequence:
          - service: scene.turn_on
            target:
              entity_id: "scene.{{ scene_full_name }}"
          - service: scene.delete
            target:
              entity_id: "scene.{{ scene_full_name }}"
mode: single
```

---

## ❓ Support & Feedback  
If you have any questions or suggestions, feel free to:  
- Comment on the **GitHub Issues** page  
- Join the **Home Assistant Community** discussions  

🚀 Enjoy your smart home automation with **Presence Sensor Blueprint**! 🎉  

