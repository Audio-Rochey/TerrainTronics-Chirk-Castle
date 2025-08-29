# Chirk Castle — DC Motor Driver H-Bridge

A very small, 0.1″-header friendly DC motor driver for hobby projects.
<img src="chirkcastlerender.png" width="50%" height="50%">
- **Designer/Manufacturer:** TerrainTronics.com
- **Board size:** ~18 mm × 14 mm (17.8 × 13.4 mm); **no mounting holes**
- **Connectors:** 0.1″ header holes or large solder-pads
- **Logic level:** 3.3–5 V tolerant (BSS138 level-shift)
- **Motor driver IC:** L9110S-HXY dual H-bridge (pre-assembled)
- **Status LEDs:** Optional (underside) for **VLOGIC** and **VMOTOR**
- **On-board protection:** **None** (no reverse-polarity/short/thermal) — protect externally

---

## Pinout (Top-View)
<img src="chirkcastlerender.png" width="50%" height="50%">
**Left 5-pin header (top → bottom):**

| Pin (top→bottom) | Net     | Function                |
|---:|:--------|:-------------------------|
| 1  | **INB** | Logic input (to H-bridge) |
| 2  | **INA** | Logic input (to H-bridge) |
| 3  | **VLOGIC** | 3.3–5 V logic rail |
| 4  | **DGND** | Ground (logic & motor) |
| 5  | **VMOTOR** | Motor supply (≤ **6.5 V**) |

**Top 2-pin motor header (left → right):** **OB**, **OA**

> Keep **DGND** common between logic and motor supplies.

---

## Electrical Limits (Board Rating)

- **VMOTOR:** up to **6.5 V max** (board design limit). Recommended **5 V** and match to motor.  
- **Current:** **800 mA average**, **1.5 A peak** (IC capability). If current/heat is a concern, add an **external load switch, fuse, or PTC**.  
- **VLOGIC:** **3.3–5 V** (tolerant via level shifters).

> **Note:** The L9110S datasheet often shows up to 12 V. TerrainTronics intentionally rates this board to **≤ 6.5 V**.

---

## Truth Table

| INA | INB | OA | OB | Motor |
|---:|---:|:--:|:--:|:------|
| 1 | 0 | H | L | **Forward** |
| 0 | 1 | L | H | **Reverse** |
| 1 | 1 | L | L | **Brake** |
| 0 | 0 | L | L | **Brake** |

*Use PWM on **INA** or **INB** for speed control. Avoid applying PWM to both inputs simultaneously.*

---

## Wiring Examples

### Wemos D1 Mini (ESP8266, 3.3 V host)

- **VLOGIC** → 3.3 V  
- **DGND** → GND  
- **INA** → D5 (GPIO14, PWM)  
- **INB** → D6 (GPIO12, PWM)  
- **VMOTOR** → 5 V motor supply (share **GND**)  
- **OB/OA** → Motor (left pad **OB**, right pad **OA**)

### Arduino UNO (5 V host)

- **VLOGIC** → 5 V  
- **DGND** → GND  
- **INA** → D5 (PWM)  
- **INB** → D6 (PWM)  
- **VMOTOR** → 5 V motor supply (share **GND**)  
- **OB/OA** → Motor (left **OB**, right **OA**)

> If forward/reverse are inverted, swap the motor leads or swap INA/INB in firmware.

---

## Example Code Snippets

### Arduino UNO (5 V)

```cpp
// Simple forward / reverse / brake demo
const int INA = 5;   // PWM-capable
const int INB = 6;   // PWM-capable

void setup() {
  pinMode(INA, OUTPUT);
  pinMode(INB, OUTPUT);
  // Brake by default
  digitalWrite(INA, LOW);
  digitalWrite(INB, LOW);
}

void loop() {
  // Forward at 50% for 2s
  analogWrite(INA, 128);
  digitalWrite(INB, LOW);
  delay(2000);

  // Brake 0.5s
  digitalWrite(INA, LOW);
  digitalWrite(INB, LOW);
  delay(500);

  // Reverse at 75% for 2s
  digitalWrite(INA, LOW);
  analogWrite(INB, 192);
  delay(2000);

  // Brake 1s
  digitalWrite(INA, LOW);
  digitalWrite(INB, LOW);
  delay(1000);
}
