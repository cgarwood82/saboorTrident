# AWD 350 Printer Configuration

This repository contains the configuration files for an AWD 350 printer running Kalico firmware with an FYSETC H36 Combo toolhead board.

## 📁 File Structure

```
saboorTrident/
├── printer.cfg                    # Main printer configuration
├── H36_Combo_Config.cfg          # H36 toolhead board configuration
├── backup_configs/                # Previous configurations
│   ├── AWD_350_Model_TMC2240.cfg
│   ├── AWD_350_Model_TMC2240_Kalico.cfg
│   └── EBB_SB2209_Config.cfg
└── README.md                      # This file
```

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

### 2. **Verify H36 Pin Assignments**
**Status:** ⚠️ **REQUIRED - BLOCKING**

The current `H36_Combo_Config.cfg` uses generic pin assignments. You need to:

1. **Check your specific H36 board version** from the [FYSETC H36 Combo repository](https://github.com/FYSETC/H36_Combo)
2. **Verify pin assignments match your board:**
   - Extruder stepper pins (gpio18, gpio19, gpio17)
   - Temperature sensor pins (gpio28, gpio9-11 for MAX31865)
   - Fan pins (gpio14, gpio13)
   - LED pins (gpio16)
   - Heater pin (gpio7)

### 3. **Test H36 Board Configuration**
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
3. **Test RGB LEDs** on toolhead
4. **Verify fan speeds** and PWM operation

## 🔍 **VERIFICATION TASKS**

### 10. **Configuration Validation**
**Status:** ⚠️ **REQUIRED**

1. **Run Kalico configuration check:**
   ```bash
   kalico-check printer.cfg
   ```

2. **Check for any syntax errors**
3. **Verify all sections are properly defined**

### 11. **Hardware Verification**
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

## 📋 **OPTIONAL IMPROVEMENTS**

### 12. **Advanced Features**
**Status:** 🔄 **OPTIONAL**

1. **Input Shaper Configuration** (if ADXL345 is installed)
2. **Custom G-code Macros** for your workflow
3. **Network Configuration** for remote access
4. **Backup Configuration** to cloud storage

### 13. **Performance Optimization**
**Status:** 🔄 **OPTIONAL**

1. **Fine-tune acceleration settings**
2. **Optimize jerk settings**
3. **Adjust microstep settings** for your needs
4. **Configure advanced features** (pressure advance, input shaper)

## 🚀 **DEPLOYMENT CHECKLIST**

### Before First Print:
- [ ] H36 UUID configured
- [ ] Pin assignments verified
- [ ] H36 board tested with onekeytest.cfg
- [ ] Extruder calibrated
- [ ] Temperature sensors calibrated
- [ ] Pressure advance calibrated
- [ ] Bed mesh calibrated
- [ ] All fans tested
- [ ] All LEDs tested
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

### Getting Help:
- Check Kalico documentation: https://docs.kalico.gg
- Review H36 Combo documentation: https://github.com/FYSETC/H36_Combo
- Check printer logs for error messages

## 📝 **NOTES**

- This configuration is for **Kalico firmware** (Klipper fork)
- Uses **FYSETC H36 Combo** toolhead board (high-temperature capable)
- Supports **Cartographer V3** probe for bed leveling
- Includes **RGB LED control** on toolhead
- Modular configuration for easy toolhead board swapping

---

**Last Updated:** Configuration created for AWD 350 printer with H36 Combo toolhead
**Status:** ⚠️ **SETUP IN PROGRESS** - Complete all critical tasks before printing 