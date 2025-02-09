## Elevator Vibration Monitoring System

This system utilizes the ADXL345 accelerometer to monitor vibrations in an elevator. The collected vibration data is transmitted to the Blynk app, providing real-time visualization of the elevator's mechanical condition. The system is designed to detect abnormal vibrations and trigger alerts, ensuring proactive maintenance and enhancing the safety and reliability of the elevator system.

### Components

- **ADXL345 Accelerometer**: Measures the vibrations in the X, Y, and Z axes.
- **ESP32**: Handles the Wi-Fi connection and sends data to the Blynk app.
- **Blynk App**: Displays real-time accelerometer data and sends notifications for abnormal events.

---

## Code Explanation

### 1. **Blynk Setup**
```cpp
#define BLYNK_TEMPLATE_ID "TMPL3gIOe8BtA"
#define BLYNK_TEMPLATE_NAME "Elevator Smart Surveillance"
#define BLYNK_AUTH_TOKEN "lg3ir2LBohoRiJnhLopOkQqH7I18qOYf"
```
- **BLYNK_TEMPLATE_ID**: Unique identifier for the template in the Blynk app.
- **BLYNK_TEMPLATE_NAME**: Name of the template.
- **BLYNK_AUTH_TOKEN**: Authentication token for connecting your ESP32 with the Blynk app.

### 2. **Libraries and Setup**
```cpp
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_ADXL345_U.h>
#include <WiFi.h>
#include <BlynkSimpleEsp32.h>
```
- **Wire.h**: For I2C communication (used by the ADXL345).
- **Adafruit_Sensor.h** and **Adafruit_ADXL345_U.h**: Libraries for reading sensor data from the ADXL345.
- **WiFi.h**: For managing Wi-Fi connectivity on the ESP32.
- **BlynkSimpleEsp32.h**: Library to interface with the Blynk app.

### 3. **Sensor Initialization**
```cpp
Adafruit_ADXL345_Unified accel = Adafruit_ADXL345_Unified(12345);
```
- Creates an instance of the `ADXL345` sensor.

### 4. **Wi-Fi Credentials**
```cpp
char ssid[] = "***";           // Your WiFi SSID
char pass[] = "***";            // Your WiFi Password
```
- The ESP32 will connect to your Wi-Fi network using these credentials.

### 5. **Variables to Track Vibration**
```cpp
float prevX = 0, prevY = 0, prevZ = 0;
float normalX_min = -1, normalX_max = 0.6; //threhold values
float normalY_min = -1.8, normalY_max = 0; //threhold values
float normalZ_min = 9.5, normalZ_max = 11.6; //threhold values
```
- **prevX, prevY, prevZ**: Variables to store the previous vibration data to calculate the changes.
- **normalX_min, normalX_max, etc.**: Define the acceptable range for the vibration values (normal range).

### 6. **Setup Function**
```cpp
void setup() {
  Serial.begin(115200); // Start serial monitor for debugging
  if (!accel.begin()) { // Initialize ADXL345 sensor
    Serial.println("Failed to detect ADXL345. Please check wiring.");
    while (1); // Stop if sensor not found
  }
  accel.setRange(ADXL345_RANGE_2_G); // Set sensor range to +-2G
  WiFi.begin(ssid, pass); // Connect to Wi-Fi
  while (WiFi.status() != WL_CONNECTED) { // Wait until connected
    delay(1000);
    Serial.println("Connecting...");
  }
  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass); // Initialize Blynk
}
```
- Initializes the **ADXL345 accelerometer** and connects to **Wi-Fi**.
- If the accelerometer is not detected, it prints an error and halts the program.

### 7. **Loop Function**
```cpp
void loop() {
  Blynk.run(); // Run Blynk
  if (WiFi.status() != WL_CONNECTED) { // Check Wi-Fi connection
    WiFi.begin(ssid, pass); // Reconnect if disconnected
    delay(1000);
    return;
  }
  sensors_event_t event;
  accel.getEvent(&event); // Get sensor data

  float deltaX = abs(event.acceleration.x - prevX);
  float deltaY = abs(event.acceleration.y - prevY);
  float deltaZ = abs(event.acceleration.z - prevZ);
  
  // Send sensor data to Blynk app
  Blynk.virtualWrite(V1, event.acceleration.x); // X-axis
  Blynk.virtualWrite(V2, event.acceleration.y); // Y-axis
  Blynk.virtualWrite(V3, event.acceleration.z); // Z-axis

  // Debugging: Print data to Serial Monitor
  Serial.print("X: "); Serial.println(event.acceleration.x);
  Serial.print("Y: "); Serial.println(event.acceleration.y);
  Serial.print("Z: "); Serial.println(event.acceleration.z);
  Serial.print("deltaX: "); Serial.println(deltaX);
  Serial.print("deltaY: "); Serial.println(deltaY);
  Serial.print("deltaZ: "); Serial.println(deltaZ);

  // Check if values are out of range
  bool outOfRange = (event.acceleration.x < normalX_min || event.acceleration.x > normalX_max) ||
                    (event.acceleration.y < normalY_min || event.acceleration.y > normalY_max) ||
                    (event.acceleration.z < normalZ_min || event.acceleration.z > normalZ_max);

  if (outOfRange) {
    unsigned long currentTime = millis();
    if (currentTime - lastNotificationTime > notificationCooldown) {
      Serial.println("Abnormal Vibration Detected!");
      Blynk.logEvent("motor_fault_alert", "Abnormal Vibration Detected!");
      lastNotificationTime = currentTime;  // Update notification time
    }
  }

  // Update previous sensor values for next loop
  prevX = event.acceleration.x;
  prevY = event.acceleration.y;
  prevZ = event.acceleration.z;

  delay(2500); // Delay before reading values again
}
```
- **Blynk.run()**: Ensures the Blynk app is continuously running and updates the data.
- **Sensor Data Processing**: The accelerometer data is read and compared with the previous data to detect any significant changes (vibrations).
- **Data Logging**: If the values are outside the normal range, a log event is created in the Blynk app, and the data is sent to the Blynk virtual pins (V1, V2, V3).
- **Cooldown Timer**: Prevents multiple alerts within a short time (notification cooldown).

### 8. **Abnormal Vibration Alert**
```cpp
Blynk.logEvent("motor_fault_alert", "Abnormal Vibration Detected!");
```
- Sends an event to the Blynk app to log and notify the user when abnormal vibration is detected.

### 9. **Delays and Updates**
```cpp
delay(2500); // Delay before the next data read
```
- Adds a delay before the next set of accelerometer readings to prevent overwhelming the system.

---

## How to Use

1. **Set up your Blynk project**: Create a new project in the Blynk app, add virtual pins (V1, V2, V3) to display the accelerometer data, and use the provided **BLYNK_AUTH_TOKEN**.
2. **Upload the code** to your ESP32 using the Arduino IDE.
3. **Monitor the data** in the Blynk app: The X, Y, Z acceleration values will be shown in real-time. If abnormal vibrations are detected, a notification will be logged in the Blynk app.

