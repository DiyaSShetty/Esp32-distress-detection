# ESP32 IoT Distress Detection System

An ESP32-based wearable monitoring prototype that tracks heart rate, SpO2, and body temperature in real time and sends emergency SMS alerts when readings become abnormal or when a panic button is pressed.

## Features
- Real-time monitoring of heart rate, SpO2, and body temperature
- Automatic distress detection with a 20-second pre-alert window to avoid false alarms
- SMS alerts to both the user and a doctor via the Twilio API
- Manual panic button that sends an alert immediately (interrupt-driven)
- Live data logging to Firebase Realtime Database
- Finger-detection check so alerts are not triggered when the sensor isn't in use

## Hardware
| Component | Purpose | Connection |
|---|---|---|
| ESP32 | Microcontroller with Wi-Fi | - |
| MAX30102 | Heart rate and SpO2 | I2C (SDA/SCL) |
| DS18B20 | Body temperature | GPIO 4 |
| Push button | Panic alert | GPIO 13 (internal pull-up) |

## How it works
1. The ESP32 reads temperature from the DS18B20 and 100 samples from the MAX30102 each cycle.
2. If no finger is detected, the state is set to `NO_FINGER` and no distress check runs.
3. Distress is flagged when any of the following is true:
   - Heart rate above 120 bpm or below 40 bpm
   - SpO2 below 90%
   - Temperature above 39.0 °C
4. State machine: `NORMAL` → `PRE_ALERT` → `ALERT`. If distress lasts 20 seconds, the state becomes `ALERT` and an SMS is sent to the user and the doctor. If vitals recover, the system returns to `NORMAL`.
5. Pressing the panic button sends an SMS immediately, regardless of sensor readings.
6. Every cycle, the vitals and state are uploaded to Firebase Realtime Database.

## Libraries required
- Firebase ESP Client
- SparkFun MAX3010x (MAX30105)
- OneWire
- DallasTemperature

## Setup
1. Install the libraries above in the Arduino IDE and select an ESP32 board.
2. Replace the placeholders in the code with your own values:
   - Wi-Fi name and password
   - Firebase API key and database URL
   - Twilio Account SID, Auth Token, and phone numbers
3. Upload the sketch to the ESP32 and open the Serial Monitor at 115200 baud.

> **Note:** Never commit real credentials to a public repository.

## Tech stack
ESP32 · Arduino IDE · C/C++ · Firebase Realtime Database · Twilio SMS API

## Author
Diya Surendra Shetty
