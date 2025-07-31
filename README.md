# AWD 350 Printer Configuration

This repository contains the configuration files for an AWD 350 printer running Kalico firmware with an FYSETC H36 Combo toolhead board.

## 📁 File Structure

```
saboorTrident/
├── config/                        # Main configuration directory
│   ├── printer.cfg               # Main printer configuration
│   ├── H36_Combo_Config.cfg     # H36 toolhead board configuration
│   ├── fluidd.cfg                # Fluidd web interface configuration
│   ├── moonraker.conf            # Moonraker API configuration
│   ├── crowsnest.conf            # Camera configuration
│   ├── KlipperScreen.conf        # KlipperScreen configuration
│   └── macros/                   # Organized macro files
│       ├── fan_control.cfg       # Fan control macros (M106)
│       ├── led_control.cfg       # LED control macros
│       ├── bed_leveling.cfg      # Bed leveling and calibration
│       ├── filament_control.cfg  # Filament management macros
│       ├── print_control.cfg     # Print workflow macros
│       └── testing.cfg           # Testing and calibration macros
├── backup_configs/               # Previous configurations
│   ├── AWD_350_Model_TMC2240.cfg
│   ├── AWD_350_Model_TMC2240_Kalico.cfg
│   └── EBB_SB2209_Config.cfg
└── README.md                     # This file
```

## 🔧 **HARDWARE CONFIGURATION**

### **Mainboard:**
- **BTT Manta M8P V2** - Main control board
- **CAN Bus Interface:** `can0` for toolhead communication
- **CAN Bus UUID:** `289407b9eea8` (main board)

### **Mainboard Pin Assignments:**
- **X-Axis Steppers:** `PE6` (STEP), `PE5` (DIR), `PC14` (EN)
- **X1-Axis Steppers:** `PD4` (STEP), `PD3` (DIR), `PD6` (EN)
- **Y-Axis Steppers:** `PC7` (STEP), `PC8` (DIR), `PD2` (EN)
- **Y1-Axis Steppers:** `PE2` (STEP), `PE1` (DIR), `PE4` (EN)
- **Z-Axis Steppers:** `PB8` (STEP), `PB7` (DIR), `PE0` (EN)
- **Z1-Axis Steppers:** `PB4` (STEP), `PB3` (DIR), `PB6` (EN)
- **Z2-Axis Steppers:** `PG13` (STEP), `PG12` (DIR), `PG15` (EN)
- **Bed Heater:** `PA1` (SSR control)
- **Bed Thermistor:** `PB1` (ATC Semitec 104GT-2)
- **Chamber Heater:** `PA3` (HE2/SSR control)
- **Chamber Thermistor:** `PB0` (TH0/Air temperature)
- **Case Light:** `PA5` (HE3/PWM control)

### **Toolhead Board:**
- **FYSETC H36 Combo** - High-temperature capable toolhead board
- **CAN Bus UUID:** `2b59b330d003` (H36 board)

### **H36 Pin Assignments:**
- **Extruder:** `PB9` (STEP), `PB8` (DIR), `PB7` (EN), `PC14` (UART)
- **Heater:** `PA7` (Heat pin)
- **Temperature Sensors:** `PA6` (NTC), `PA3` (MAX31865 RTD)
- **Fans:** `PA5` (Hotend fan), `PB13` (Nozzle fan)
- **RGB LED:** `PB1` (IO.4/RGB1)
- **Filament Sensors:** `PA15` (Entry), `PC7` (Hotend)
- **X Endstop:** `PA8` (IO.2)
- **Cartographer V3:** Connected via CAN bus to H36 board

### **Additional Hardware:**
- **Chamber Heater:** `PA3` (HE2) with SSR control
- **Chamber Fan:** `PA6` (FAN5) - 4-pin fan
- **Chamber Thermistor:** `PB0` (TH0) - Air temperature monitoring
- **Case Light:** `PA5` (HE3) - PWM-controlled case lighting

## 🚨 **CRITICAL TASKS TO COMPLETE**

## 🔧 **CALIBRATION TASKS**

- **Extruder Calibration**
  - **Status:** **Complete**
  - The current `rotation_distance: 22.6789511` is a recommended value. You need to calibrate:
    - Measure actual extrusion:
      - Mark 100mm on filament
      - Extrude 100mm
      - Measure actual distance moved
    - Calculate new rotation_distance:
      ```
      New value = old value × (actual extruded length / target length)
      ```
      Example: If you get 98mm instead of 100mm:
      ```
      New value = 22.6789511 × (98/100) = 22.2253
      ```
    - Update `config/H36_Combo_Config.cfg` with the new value

- **Temperature Sensor Calibration**
  - **Status:**  **Complete**

## 🧪 **TESTING TASKS**

- **Bed Leveling and Mesh**
  - **Status:** **Complete**

- **Resonance Testing**
  - **Status:** **Complete**
  - Install ADXL345 on the toolhead (recommended)

- **Fan and LED Testing**
  - **Status:** **Complete**
  - Test hotend fan (starts at 50°C)
  - Test part cooling fan (5015 blower)
  - Test chamber fan (4-pin fan)
  - Test RGB LEDs on toolhead
  - Test case light control
  - Verify fan speeds and PWM operation

- **Filament Sensor Testing**
  - **Status:** **Complete**
  - Test entry filament sensor (PA15)
  - Test hotend filament sensor (PC7)
  - Verify MMU compatibility for tool changes
  - Test sensor response during filament loading/unloading

## 🔍 **VERIFICATION TASKS**

- **Hardware Verification**
  - **Status:** **Complete**
  - Test all stepper motors:
    - X, X1, Y, Y1, Z, Z1, Z2 axes
    - Extruder motor
  - Test all endstops:
    - X, Y, Z limit switches
  - Test all temperature sensors:
    - Bed thermistor
    - Chamber thermistor
    - Extruder RTD sensor
  - Test all heaters:
    - Bed heater
    - Extruder heater
    - Chamber heater

## 📋 **OPTIONAL IMPROVEMENTS**

- **Advanced Features**
  - **Status:** 🔄 **OPTIONAL**
  - Input Shaper Configuration (if ADXL345 is installed)
2. **Custom G-code Macros** for your workflow
3. **Network Configuration** for remote access
4. **Backup Configuration** to cloud storage

### 9. **Performance Optimization**
**Status:** 🔄 **OPTIONAL**

1. **Fine-tune acceleration settings**
2. **Optimize jerk settings**
3. **Adjust microstep settings** for your needs
4. **Configure advanced features** (pressure advance, input shaper)

## 🔧 **FINAL CALIBRATION TASKS**

### 10. **Chamber Heater Calibration**
**Status:** ⚠️ **REQUIRED - FINAL STEP**

**Note:** This should be done after all panels are installed and the printer is fully assembled.

The chamber heater needs PID calibration:

```bash
PID_CALIBRATE HEATER=chamber_heater TARGET=60
```

**Update `config/printer.cfg`** with the new PID values after calibration.

## 🚀 **DEPLOYMENT CHECKLIST**

### After First Print:
- [ ] Fine-tune pressure advance
- [ ] Adjust acceleration/jerk if needed
- [ ] Configure input shaper (if ADXL345 available)
- [ ] Create custom macros for your workflow

## 📝 **NOTES**

- This configuration is for **Kalico firmware** (Klipper fork)
- Uses **FYSETC H36 Combo** toolhead board (high-temperature capable)
- Supports **Cartographer V3** probe for bed leveling
- Includes **RGB LED control** on toolhead
- **Modular macro organization** for easy maintenance
- **Chamber heating system** for temperature control
- **Case lighting** for better visibility
- **Filament sensors** for MMU compatibility
- **Modular configuration** for easy toolhead board swapping
- **Configuration files** organized in `config/` directory
