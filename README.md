# AWD 350 Printer Configuration

This repository contains the configuration files for an AWD 350 printer running Kalico firmware with an FYSETC H36 Combo toolhead board.

## 📁 File Structure

```
saboorTrident/
├── printer.cfg                    # Main printer configuration
├── H36_Combo_Config.cfg          # H36 toolhead board configuration
├── macros/                        # Organized macro files
│   ├── fan_control.cfg           # Fan control macros (M106)
│   ├── led_control.cfg           # LED control macros
│   ├── bed_leveling.cfg          # Bed leveling and calibration
│   ├── filament_handling.cfg     # Filament management macros
│   ├── print_control.cfg         # Print workflow macros
│   └── testing.cfg               # Testing and calibration macros
├── backup_configs/                # Previous configurations
│   ├── AWD_350_Model_TMC2240.cfg
│   ├── AWD_350_Model_TMC2240_Kalico.cfg
│   └── EBB_SB2209_Config.cfg
└── README.md                      # This file
```

## 🔧 **HARDWARE CONFIGURATION**

### **Mainboard:**
- **BTT Manta M8P V2** - Main control board
- **CAN Bus Interface:** `can0` for toolhead communication
- **CAN Bus UUID:** `41cbab4642d7` (main board)

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
- **CAN Bus UUID:** Needs to be configured (see Critical Tasks)

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

### 1. **Get H36 CAN Bus UUID**
**Status:** ⚠️ **REQUIRED - BLOCKING**

You need to get the actual CAN bus UUID for your H36 board:

```bash
sudo /usr/local/bin/klippy-env/bin/python /home/pi/klipper/scripts/canbus_query.py can0
```

**Then update `H36_Combo_Config.cfg`:**
- Replace `canbus_uuid: YOUR_H36_UUID_HERE` with your actual UUID
- Example: `canbus_uuid: 2733cea0ce24`

### 2. **Test H36 Board Configuration**
**Status:** ⚠️ **REQUIRED - BLOCKING**

Before using the main configuration:

1. **Download the test config:**
   ```bash
   wget https://raw.githubusercontent.com/FYSETC/H36_Combo/main/H36_combo_onekeytest.cfg
   ```

2. **Test the H36 board:**
   - Upload `H36_combo_onekeytest.cfg` to your printer
   - Verify all components work (extruder, fans, temperature sensors, LEDs)
   - Check for any error messages

3. **Only proceed to main config after successful testing**

### 3. **Chamber Heater Calibration**
**Status:** ⚠️ **REQUIRED - NEW**

The chamber heater needs PID calibration:

```bash
PID_CALIBRATE HEATER=chamber_heater TARGET=60
```

**Update `printer.cfg`** with the new PID values after calibration.

## 🔧 **CALIBRATION TASKS**

### 4. **Extruder Calibration**
**Status:** ⚠️ **REQUIRED**

The current `rotation_distance: 22.6789511` is a recommended value. You need to calibrate:

1. **Measure actual extrusion:**
   - Mark 100mm on filament
   - Extrude 100mm
   - Measure actual distance moved

2. **Calculate new rotation_distance:**
   ```
   New value = old value × (actual extruded length / target length)
   ```
   Example: If you get 98mm instead of 100mm:
   ```
   New value = 22.6789511 × (98/100) = 22.2253
   ```

3. **Update `H36_Combo_Config.cfg`** with the new value

### 5. **Temperature Sensor Calibration**
**Status:** ⚠️ **REQUIRED**

For the MAX31865 RTD sensor:

1. **PID Calibration:**
   ```bash
   PID_CALIBRATE HEATER=extruder TARGET=245
   ```

2. **Verify temperature readings** are accurate
3. **Check for sensor errors** in the logs

### 6. **Pressure Advance Calibration**
**Status:** ⚠️ **REQUIRED**

Current value: `pressure_advance: 0.05`

1. **Run pressure advance test:**
   ```bash
   TEST_RESONANCES AXIS=X
   TEST_RESONANCES AXIS=Y
   ```

2. **Adjust pressure_advance** in `H36_Combo_Config.cfg`
3. **Keep pressure advance below 1.0** for best results

## 🧪 **TESTING TASKS**

### 7. **Bed Leveling and Mesh**
**Status:** ⚠️ **REQUIRED**

1. **Test bed mesh calibration:**
   ```bash
   BED_MESH_CALIBRATE
   ```

2. **Test Z-tilt adjustment:**
   ```bash
   Z_TILT_ADJUST
   ```

3. **Verify probe accuracy** with Cartographer V3

### 8. **Resonance Testing**
**Status:** ⚠️ **REQUIRED**

1. **Install ADXL345** on the toolhead (recommended)
2. **Run resonance tests:**
   ```bash
   SHAPER_CALIBRATE
   ```

3. **Apply shaper settings** to reduce vibrations

### 9. **Fan and LED Testing**
**Status:** ⚠️ **REQUIRED**

1. **Test hotend fan** (starts at 50°C)
2. **Test part cooling fan** (5015 blower)
3. **Test chamber fan** (4-pin fan)
4. **Test RGB LEDs** on toolhead
5. **Test case light** control
6. **Verify fan speeds** and PWM operation

### 10. **Filament Sensor Testing**
**Status:** ⚠️ **REQUIRED**

1. **Test entry filament sensor** (PA15)
2. **Test hotend filament sensor** (PC7)
3. **Verify MMU compatibility** for tool changes
4. **Test sensor response** during filament loading/unloading

## 🔍 **VERIFICATION TASKS**

### 11. **Configuration Validation**
**Status:** ⚠️ **REQUIRED**

1. **Run Kalico configuration check:**
   ```bash
   kalico-check printer.cfg
   ```

2. **Check for any syntax errors**
3. **Verify all sections are properly defined**

### 12. **Hardware Verification**
**Status:** ⚠️ **REQUIRED**

1. **Test all stepper motors:**
   - X, X1, Y, Y1, Z, Z1, Z2 axes
   - Extruder motor

2. **Test all endstops:**
   - X, Y, Z limit switches

3. **Test all temperature sensors:**
   - Bed thermistor
   - Chamber thermistor
   - Extruder RTD sensor

4. **Test all heaters:**
   - Bed heater
   - Extruder heater
   - Chamber heater

## 📋 **OPTIONAL IMPROVEMENTS**

### 13. **Advanced Features**
**Status:** 🔄 **OPTIONAL**

1. **Input Shaper Configuration** (if ADXL345 is installed)
2. **Custom G-code Macros** for your workflow
3. **Network Configuration** for remote access
4. **Backup Configuration** to cloud storage

### 14. **Performance Optimization**
**Status:** 🔄 **OPTIONAL**

1. **Fine-tune acceleration settings**
2. **Optimize jerk settings**
3. **Adjust microstep settings** for your needs
4. **Configure advanced features** (pressure advance, input shaper)

## 🚀 **DEPLOYMENT CHECKLIST**

### Before First Print:
- [ ] H36 UUID configured
- [ ] H36 board tested with onekeytest.cfg
- [ ] Chamber heater PID calibrated
- [ ] Extruder calibrated
- [ ] Temperature sensors calibrated
- [ ] Pressure advance calibrated
- [ ] Bed mesh calibrated
- [ ] All fans tested
- [ ] All LEDs tested
- [ ] Case light tested
- [ ] Filament sensors tested
- [ ] Configuration validated with kalico-check
- [ ] All steppers and endstops tested

### After First Print:
- [ ] Fine-tune pressure advance
- [ ] Adjust acceleration/jerk if needed
- [ ] Configure input shaper (if ADXL345 available)
- [ ] Create custom macros for your workflow

## 📞 **TROUBLESHOOTING**

### Common Issues:
1. **CAN Bus Connection:** Check UUID and physical connections
2. **Temperature Errors:** Verify sensor wiring and calibration
3. **Stepper Issues:** Check pin assignments and current settings
4. **Fan Problems:** Verify PWM settings and wiring
5. **Chamber Heater:** Check SSR wiring and PID calibration
6. **Filament Sensors:** Verify MMU compatibility and sensor response

### Getting Help:
- Check Kalico documentation: https://docs.kalico.gg
- Review H36 Combo documentation: https://github.com/FYSETC/H36_Combo
- Check printer logs for error messages

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

---

**Last Updated:** Configuration updated with H36 Combo toolhead, chamber heater, case light, and organized macro structure
**Status:** ⚠️ **SETUP IN PROGRESS** - Complete all critical tasks before printing 