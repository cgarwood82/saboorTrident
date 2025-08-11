# AWD 350 Printer Configuration

This repository contains the configuration files for an AWD 350 printer running Kalico firmware with a BTT Manta M8P V2 mainboard and FYSETC H36 Combo toolhead board.

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