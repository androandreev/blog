+++
date = '2025-01-05T15:45:24+01:00'
draft = false
title = 'Kettler to Strava on Esp32'
+++

# Connecting a Kettler Crosstrainer CTR3 to Strava with an ESP32

If you own a Kettler Crosstrainer CTR3 and are eager to track your workouts on Strava, you're not alone. Despite its advanced features, the CTR3 lacks native Strava integration. However, with some creativity and technology, you can bridge this gap using its serial port and an ESP32 microcontroller. Here's how you can make it happen.

## What You'll Need

- **Kettler Crosstrainer CTR3**
- **ESP32 microcontroller**
- **Serial-to-TTL adapter** (if needed, to connect the serial port)
- **USB cable and power supply**
- **Basic soldering tools** (optional, depending on your setup)
- **Programming environment** (Arduino IDE or PlatformIO)

## The Concept

The Kettler CTR3 outputs workout data via a serial port, typically using a simple protocol to communicate speed, distance, calories, and heart rate. By connecting the ESP32 to the serial port, you can capture this data and format it for transmission to Strava via its API.

### Key Steps

1. **Identify the Serial Port**
   Locate the serial port on the CTR3. It may be labeled in the user manual or on the device itself. Ensure you have the appropriate adapter to connect the port to the ESP32.

2. **Capture the Data**
   Use the ESP32 to read data from the CTR3. Most Kettler devices use standard baud rates (e.g., 9600 bps). You'll need to decode the protocol, which often involves parsing simple ASCII strings.

3. **Process the Data**
   Write a program for the ESP32 to process the data. Extract key metrics like speed, distance, and heart rate, and format them for compatibility with Strava.

4. **Send Data to Strava**
   Use the ESP32’s Wi-Fi capability to send the workout data to Strava. This requires implementing the Strava API, which involves:
   - Registering for a Strava Developer account
   - Obtaining an API access token
   - Sending activity data as a POST request

5. **Test and Refine**
   Test your setup thoroughly to ensure accurate data transmission. You might need to fine-tune parsing logic or API calls.

## Example Code
Here’s a simplified example of how you might start:

```cpp
#include <WiFi.h>
#include <HTTPClient.h>

// Wi-Fi credentials
const char* ssid = "Your_SSID";
const char* password = "Your_PASSWORD";

// Strava API endpoint
const char* strava_endpoint = "https://www.strava.com/api/v3/uploads";
const char* access_token = "Your_Access_Token";

void setup() {
    Serial.begin(9600); // For CTR3 serial communication
    WiFi.begin(ssid, password);

    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to Wi-Fi...");
    }
    Serial.println("Connected to Wi-Fi");
}

void loop() {
    if (Serial.available()) {
        String data = Serial.readString();
        Serial.println("Data received: " + data);

        // Process and format data for Strava (not implemented here)

        // Example HTTP request to Strava
        HTTPClient http;
        http.begin(strava_endpoint);
        http.addHeader("Authorization", "Bearer " + String(access_token));
        http.addHeader("Content-Type", "application/json");

        int httpResponseCode = http.POST("{\"name\":\"Workout\",\"type\":\"Ride\"}");

        if (httpResponseCode > 0) {
            Serial.println("Data sent to Strava");
        } else {
            Serial.println("Error sending data");
        }

        http.end();
    }
}
```

## Challenges and Tips

- **Understanding the Protocol**: If the serial protocol is undocumented, you'll need to reverse-engineer it. Use a serial monitor to inspect the raw output.
- **Strava API Limits**: Strava's API has rate limits, so plan your data uploads accordingly.
- **Power Management**: Ensure your ESP32 is properly powered to handle Wi-Fi transmission.

## Conclusion

Connecting your Kettler Crosstrainer CTR3 to Strava is a rewarding project that enhances your workout experience. With an ESP32 and some programming skills, you can track your progress seamlessly. Happy hacking!