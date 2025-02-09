Elevator Smart Surveillance System kkj

Project Overview

EleSense is a real-time monitoring system designed to detect faults in elevator motors and door mechanisms using vibration and sound analysis. The system integrates an ADXL345 accelerometer and an INMP441 MEMS microphone with an ESP32-S3 microcontroller to analyze abnormal vibrations and door sounds, triggering notifications via Blynk.

Features

Vibration Monitoring: Detects abnormal vibration patterns in the elevator motor.

Sound Analysis: Identifies abnormal door closing sounds exceeding 14.3 kHz.

Real-Time Notifications: Sends alerts to a mobile app using Blynk.

Wireless Data Transmission: Uses ESP32-S3 for seamless data logging and alerts.

Components Used
void checkDoorSound() {
  int16_t sampleBuffer[SAMPLES];  // Buffer to hold raw audio data
  size_t bytesRead;

  // Read audio samples from the microphone
  i2s_read(I2S_NUM_0, sampleBuffer, sizeof(sampleBuffer), &bytesRead, portMAX_DELAY);

  // Convert audio samples to frequency domain using FFT
  for (int i = 0; i < SAMPLES; i++) {
    vReal[i] = sampleBuffer[i];
    vImag[i] = 0;
  }

  FFT.Windowing(vReal, SAMPLES, FFT_WIN_TYP_HAMMING, FFT_FORWARD);
  FFT.Compute(vReal, vImag, SAMPLES, FFT_FORWARD);
  FFT.ComplexToMagnitude(vReal, vImag, SAMPLES);

  // Find the peak frequency
  double peakFrequency = FFT.MajorPeak(vReal, SAMPLES, 44100);
  Serial.print("Peak Frequency: ");
  Serial.println(peakFrequency);

  // Trigger a notification if peak frequency exceeds 14.3 kHz
  if (peakFrequency > 14300) {
    Serial.println("Abnormal Door Sound Detected! Logging Event...");
    Blynk.logEvent("door_fault_alert", "Abnormal Door Sound Detected! Frequency exceeds 14.3 kHz.");
  } else {
    Serial.println("Normal Door Sound Detected");
  }

  delay(1000);  // Delay for audio readings
}

ESP32-S3

ADXL345 Accelerometer

INMP441 MEMS Microphone

Power Supply (5V/3.3V)

Connecting Wires

Circuit Diagram

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

