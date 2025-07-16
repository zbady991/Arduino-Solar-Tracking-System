# 🌞 Arduino Solar Tracking System

An automated dual-axis **solar tracker** built using Arduino, servo motors, and LDR sensors. This system intelligently adjusts its angle to follow the sun’s position, maximizing solar energy capture.

---

## 🛠️ Features

- Real-time light tracking using 4 LDR sensors
- Dual-axis control using two servo motors
- Accurate positioning of solar panel using analog sensor data
- Compact and low-power implementation for educational or real-world use

---

## 🔧 Components Used

- Arduino Uno
- 2x Servo Motors (for horizontal and vertical movement)
- 4x LDR (Light Dependent Resistors)
- 4x 10kΩ Resistors
- Breadboard + Jumper wires
- USB Cable (for programming & power)

---

## 📸 Circuit Diagram

!![Wiring Diagram](IMAGES/Mighty Migelo (1).png)


---

## 💡 Code Overview

The logic reads 4 LDRs placed in a cross layout and calculates:
- **Top vs. Bottom** light difference → moves vertical servo
- **Left vs. Right** light difference → moves horizontal servo  
Servo limits and tolerance are used to ensure stable movement and avoid jitter.

### `SmartTracker.ino` (Core Logic)

```cpp
#include <Servo.h>  

// Servo Initialization
Servo horizontal;
Servo vertical;

// Servo Position & Limits
int servohori = 180;
int servovert = 45;
int servohoriLimitHigh = 175, servohoriLimitLow = 5;
int servovertLimitHigh = 100, servovertLimitLow = 1;

// LDR Pins
int ldrlt = A0, ldrrt = A3;
int ldrld = A1, ldrrd = A2;

void setup() {
  horizontal.attach(2);
  vertical.attach(13);
  horizontal.write(180);
  vertical.write(45);
  delay(2500);
}

void loop() {
  int lt = analogRead(ldrlt);
  int rt = analogRead(ldrrt);
  int ld = analogRead(ldrld);
  int rd = analogRead(ldrrd);

  int dtime = 10;
  int tol = 90;

  int avt = (lt + rt) / 2;
  int avd = (ld + rd) / 2;
  int avl = (lt + ld) / 2;
  int avr = (rt + rd) / 2;

  int dvert = avt - avd;
  int dhoriz = avl - avr;

  if (abs(dvert) > tol) {
    if (avt > avd) servovert = min(++servovert, servovertLimitHigh);
    else servovert = max(--servovert, servovertLimitLow);
    vertical.write(servovert);
  }

  if (abs(dhoriz) > tol) {
    if (avl > avr) servohori = max(--servohori, servohoriLimitLow);
    else servohori = min(++servohori, servohoriLimitHigh);
    horizontal.write(servohori);
  }

  delay(dtime);
}
