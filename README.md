Elevator Smart Surveillance System

Project Overview

EleSense is a real-time monitoring system designed to detect faults in elevator motors and door mechanisms using vibration and sound analysis. The system integrates an ADXL345 accelerometer and an INMP441 MEMS microphone with an ESP32-S3 microcontroller to analyze abnormal vibrations and door sounds, triggering notifications via Blynk.

Features

Vibration Monitoring: Detects abnormal vibration patterns in the elevator motor.

Sound Analysis: Identifies abnormal door closing sounds exceeding 14.3 kHz.

Real-Time Notifications: Sends alerts to a mobile app using Blynk.

Wireless Data Transmission: Uses ESP32-S3 for seamless data logging and alerts.

Components Used

ESP32-S3

ADXL345 Accelerometer

INMP441 MEMS Microphone

Power Supply (5V/3.3V)

Connecting Wires

Circuit Diagram

(Add your project image here)

ESP32 Code

(Add your ESP32 code here)

Setup Instructions

Hardware Connections:

Connect ADXL345 to ESP32 via I2C (SDA, SCL).

Connect INMP441 MEMS microphone via I2S (DATA, WS, SCK).

Power the sensors using 3.3V/5V from ESP32.

Software Configuration:

Install Arduino IDE and necessary libraries (Adafruit ADXL345, Blynk, ESP32 I2S).

Configure WiFi and Blynk credentials.

Upload the ESP32 code.

Blynk Setup:

Create a new Blynk project and add virtual pins for sensor readings.

Configure automation to send notifications for abnormal vibration or sound levels.

Notification Criteria

Vibration Alert: If accelerometer values exceed defined safe thresholds.

Sound Alert: If the detected frequency surpasses 14.3 kHz.

Future Enhancements

Integrate an ML model for more accurate fault detection.

Extend monitoring to elevator rope and pulley systems.

Author

Developed by Shivani and team.

(Modify this README as needed. Let me know if you want additional sections!)

