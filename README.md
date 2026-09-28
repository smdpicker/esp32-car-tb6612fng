# ESP32 BLE Proportional RC Car Controller

High-performance, real-time Bluetooth Low Energy (BLE) firmware designed for custom RC vehicles. Maintained by [SMDPicker](https://smdpicker.com).

Built for the **ESP32 DevKitV1** and **TB6612FNG** dual H-Bridge driver, this repository implements direct GATT write callbacks and 2-byte binary payloads (`WRITE_NR`) for sub-10ms response times and smooth proportional control.

---

## 🛠 Features

- **Proportional Control:** Precise variable torque and throttle response using dynamic PWM mapping.
- **Sub-10ms Latency:** Zero-copy binary GATT packet processing (`WRITE_NR`) executing inside BLE radio interrupts.
- **Hardware-Tuned Speed Curves:** Non-linear hardware scaling eliminates dead zones across different driver ICs and gearboxes.
- **Failsafe Watchdog:** Auto-stops drive and steering systems if BLE packets stall for > 1000ms.
- **Brownout Safeguard:** Disables software BOD resets during RF current transients.

---

---

## REPOSITORY CONTENT

---

- rc_car_firmware.ino : Arduino code for the ESP32 board.
- controller.apk : Ready-to-install Android App for controlling
  the RC car.
- README.txt : Project instructions and guide (this file).

## 🔌 Hardware Wiring Diagram

| ESP32 Pin       | TB6612FNG Pin | Module / Function | Description             |
| :-------------- | :------------ | :---------------- | :---------------------- |
| **GPIO 32**     | `PWMA`        | Motor A PWM       | Acceleration Control    |
| **GPIO 26**     | `AIN1`        | Motor A Direction | Direction Bit 1         |
| **GPIO 27**     | `AIN2`        | Motor A Direction | Direction Bit 2         |
| **GPIO 33**     | `PWMB`        | Motor B PWM       | Steering Torque Control |
| **GPIO 12**     | `BIN1`        | Motor B Direction | Direction Bit 1         |
| **GPIO 13**     | `BIN2`        | Motor B Direction | Direction Bit 2         |
| **GPIO 14**     | `STBY`        | Standby Control   | Driver Enable (`HIGH`)  |
| **3.3V**        | `VCC`         | Logic Power       | Logic Voltage Supply    |
| **GND**         | `GND`         | System Ground     | Common Ground           |
| **External V+** | `VM`          | Motor Power       | Power Supply (6V - 12V) |

> **Hardware Tip:** Connect a **100µF – 470µF electrolytic capacitor** directly between `VIN` and `GND` on the ESP32 to smooth RF power draw during startup.

---

## ⚙ PWM Speed Calibration

To account for mechanical resistance and starting torque thresholds, raw `1–255` client inputs map directly to hardware PWM output ranges:

| Direction       | Command Byte | Raw Input Range | Mapped Hardware PWM Output |
| :-------------- | :----------- | :-------------- | :------------------------- |
| **Forward**     | `'F'`        | `1` – `255`     | **`155`** – **`255`** PWM  |
| **Reverse**     | `'B'`        | `1` – `255`     | **`135`** – **`185`** PWM  |
| **Steer Left**  | `'L'`        | `1` – `255`     | **`145`** – **`215`** PWM  |
| **Steer Right** | `'R'`        | `1` – `255`     | **`145`** – **`215`** PWM  |

---

## 📡 Mobile App Integration Protocol

For custom web, Flutter, or native mobile clients, use the Nordic UART RX Characteristic (`6E400002-B5A3-F393-E0A9-E50E24DCCA9E`).

### Binary Packet Format

- **Byte 0:** Command ASCII character (`'F'`, `'B'`, `'S'`, `'L'`, `'R'`, `'D'`)
- **Byte 1:** Value Byte (`0` to `255`)

### Command Reference

| Action           | Byte 0 | Byte 1 Range | Example Byte Array        |
| :--------------- | :----- | :----------- | :------------------------ |
| Forward Throttle | `'F'`  | `0` – `255`  | `[0x46, 0x96]` (Val: 150) |
| Reverse Throttle | `'B'`  | `0` – `255`  | `[0x42, 0xC8]` (Val: 200) |
| Stop Drive       | `'S'`  | `0`          | `[0x53, 0x00]`            |
| Steer Left       | `'L'`  | `0` – `255`  | `[0x4C, 0x80]` (Val: 128) |
| Steer Right      | `'R'`  | `0` – `255`  | `[0x52, 0xFF]` (Val: 255) |
| Center Steering  | `'D'`  | `0`          | `[0x44, 0x00]`            |

---

## 🚀 Getting Started

1. Open **Arduino IDE** (v2.x+ recommended).
2. Install **ESP32 Board Support Package** (Espressif Systems v3.x).
3. Install **NimBLE-Arduino** via the Library Manager (v2.0.0 or higher).
4. Select **ESP32 Dev Module** under **Tools > Board**.
5. Compile and flash `rc_car_firmware.ino`.

---

### HOW TO INSTALL AND USE THE CONTROLLER APP

1. Copy the **controller.apk** file from this repository to your Android phone.
2. Tap the APK file on your phone to install it. (Allow "Install from Unknown Sources" if asked).
3. Turn ON Bluetooth and Location on your mobile phone.
4. Power ON your ESP32 RC Car.
5. Open the installed App and connect to **"ESP32_BLE_Car"**.
6. Use the steering wheel and gas pedal on the screen to control your car!

---

## PROJECT CREDITS

Developed and maintained by SMDPicker (https://smdpicker.com)
Free for educational and learning purposes!

---

## 📄 License

Distributed under the MIT License. Developed for open electronics and robotics projects by [SMDPicker](https://smdpicker.com).
