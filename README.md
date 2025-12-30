# 🚗 AutoLane: IoT-Connected Autonomous Rover

![Python](https://img.shields.io/badge/Vision-OpenCV-green)
![Cloud](https://img.shields.io/badge/Cloud-Firebase-orange)
![Hardware](https://img.shields.io/badge/Firmware-Arduino%20%7C%20ESP8266-blue)

## 📌 Project Overview
**AutoLane** is a prototype for a self-driving vehicle that utilizes **Computer Vision** for lane keeping and **Cloud Computing** for remote control logic.

The system captures video from an onboard camera, processes it on a host machine to calculate the optimal steering angle, and transmits control commands via **Firebase** to the vehicle in real-time. It also features **RFID Authentication** for ignition and voltage monitoring for battery safety.

## ⚙️ System Architecture

### 1. 👁️ Vision Subsystem (Python & OpenCV)
* **Input:** Streams video from an IP Camera mounted on the car.
* **Processing:**
    * Converts frames to HSV and applies masking for lane color.
    * Uses **Canny Edge Detection** and **Hough Transform** to identify lane lines.
    * Calculates the steering angle based on the lane curvature.
* **Output:** Updates the steering decision (`dir_auto`) to the **Firebase Realtime Database**.

### 2. ☁️ IoT Bridge (ESP8266)
* Connects to the local WiFi and listens to changes in the Firebase Database.
* Retrives the steering command (Left, Right, Forward, Stop).
* Transmits the command to the Arduino via GPIO/Serial.

### 3. 🦾 Control Subsystem (Arduino)
* **Actuation:** Controls DC motors via an H-Bridge driver based on signals from the ESP8266.
* **Security:** Requires an authorized **RFID Tag** scan (MFRC522) to start the system.
* **Safety:** Monitors battery voltage via analog pins and halts if levels are critical.

## 🛠️ Hardware Stack
* **Microcontrollers:** Arduino Uno + ESP8266 (NodeMCU/WeMos).
* **Sensors:** MFRC522 RFID, Voltage Sensors, IP Camera (Smartphone or ESP32-CAM).
* **Actuators:** 4x DC Motors + Motor Driver (L298N).
* **Connectivity:** WiFi (2.4GHz).

## 🚀 How to Run
1.  **Cloud Setup:** Create a Firebase Realtime DB and update credentials in `esp_firebase.ino` and `lane_pilot.py`.
2.  **Firmware:**
    * Upload `arduino_main.ino` to the Arduino.
    * Upload `esp_firebase.ino` to the ESP8266.
3.  **Vision Engine:**
    * Connect to the IP Camera stream.
    * Run the Python script:
      ```bash
      python computer_vision/lane_pilot.py
      ```
4.  **Start:** Swipe the RFID card to unlock the car and watch it drive!

## 📸 Code Snippet (Lane Detection)
```python
# From lane_pilot.py
def steering_angle(img, lines):
    # logic to calculate angle based on lane lines
    # ...
    angle_deg = int(angle_radian * 180.0 / math.pi)
    return angle_deg