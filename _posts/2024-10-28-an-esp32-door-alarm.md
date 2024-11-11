---
title: "Building a cheap door alarm with an ESP32 and AWS IoT"
permalink: /esp32-door-alarm/
categories:
  - Blog
tags:
  - aws
  - iot
  - esp32
---

A few years ago I had a shed built on my property, well perhaps a barn is a better description. Whilst I live in a good area, I'm always concious of security and decided to build a solution to notify me if the doors are opened. I could have bought an off the shelf solution, but where's the fun in that!

![](/assets/post_images/2024/esp32dooralarm/reedsensor.gif)


## At a glance
The esp32 is the brains of the system, it's connected to a reed switch that will be triggered when the door opens or closes, which then sends a message to AWS IoT via MQTT. I have some rules configured in AWS IoT that forward the message on to a time series DB so I can plot the opening/closing in Grafana, but the rule also forwards to SNS so my phone will be alerted if the door opens. After a while I did replace the SNS rule with a Lambda that posts to my discord server instead, because SMS becomes expensive, at least in terms of hobby projects.

<!--more-->

## Hardware
The parts I used:
1. ESP32
2. Pack of reed switches
3. Breadboard
4. Jumper wires
5. Extra length of wire because the sensor was a long way from the ESP32
6. An old plastic tub to house it, in my case, a cleaned out food container, but a butter/yoghurt tub would work too.

The wiring was very simple, the reed switch has two wires, one goes to ground and the other to a GPIO pin on the ESP32, I used GPIO 23. The reed switch is normally open, so when the magnet is close, the circuit is closed and the GPIO pin goes high.

![](/assets/post_images/2024/esp32dooralarm/reedsensor.jpg)

Installation was also easy, I just used some double sided tape to stick the reed switch to the door frame and the magnet to the door, I had to use a bit of extra wire to reach the ESP32, but it was easy to hide behind the framing of the shed.

![](/assets/post_images/2024/esp32dooralarm/reedsensorondoor.png)
![](/assets/post_images/2024/esp32dooralarm/doorsensorenclosure.png)

## Software

The software is also rather straightforward, but do note that I also had a BMP280 sensor connected to the ESP32, so you'll see some references to that in the code. I was using this board for both the door sensor and temperature sensor.

```cpp
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP280.h>
#include "utils.h"

// Pin wiring:
//    VCC -- 3v
//    GND -- GND
//    SCL -- 22
//    SDA -- 21

#define SEALEVELPRESSURE_HPA (961.77) // Replace with the actual sea level pressure for your area

Adafruit_BMP280 bmp; // I2C

unsigned long lastHeartbeat = 0;
unsigned long lastTemperatureReading = 0;
const int heartbeatInterval = 60000;  // Heartbeat interval in milliseconds
const int temperatureReadingInterval = 60000;  // Temperature reading interval in milliseconds

const int reedSwitchPin1 = 19;  // Define the digital pin connected to the reed switch
int reedSwitch1State = 0;
const String doorName = "pa-door";

void setup() {
  Serial.begin(9600);
  
  if (!bmp.begin(0x76)) {
    Serial.println("Could not find a valid BMP280 sensor, check wiring!");
    while (1);
  }

  pinMode(reedSwitchPin1, INPUT_PULLUP);  // Set the reed switch pin as an input with internal pull-up resistor
  reedSwitch1State = digitalRead(reedSwitchPin1); // set the initial state of the reed switch
  connectAWS();
}

void loop() {

  // check reed switch
  int reedSwitchState1Loop = digitalRead(reedSwitchPin1);  // Read the state of the reed switch
  
  // if the door was closed, but now opened
  if (reedSwitch1State == LOW && reedSwitchState1Loop == HIGH){
    Serial.println("Door opened");
    publishDoorTransition(doorName, "OPEN");
    reedSwitch1State = reedSwitchState1Loop;
  }
   else if (reedSwitch1State == HIGH && reedSwitchState1Loop == LOW) {
    Serial.println("Door closed");
    publishDoorTransition(doorName, "CLOSE");
    reedSwitch1State = reedSwitchState1Loop;
   }


  // Send heartbeat every 5 seconds
  if (millis() - lastHeartbeat > heartbeatInterval) {
    publishHeartbeat();
    lastHeartbeat = millis();
  }


  // Take temperature reading every 30 seconds
  if (millis() - lastTemperatureReading > temperatureReadingInterval) {

    Serial.println("Reading BMP280...");

    float temperature = bmp.readTemperature();
    float pressure = bmp.readPressure() / 100.0F;

    Serial.print("Temperature = ");
    Serial.print(temperature);
    Serial.println(" *C");

    Serial.print("Pressure = ");
    Serial.print(pressure);
    Serial.println(" hPa");

    float altitude = bmp.readAltitude(SEALEVELPRESSURE_HPA);
    Serial.print("Approx. Altitude = ");
    Serial.print(altitude);
    Serial.println(" m");

    Serial.println();

    publishMessage(temperature, pressure);  

    lastTemperatureReading = millis();
  }


  // Handle MQTT events
  if (!client.connected()) {
    connectAWS();
  }
  client.loop();
}

```

I also moved some code over to a `utils.h` file that handles the connectivity to AWS

```cpp
#include <Arduino_BuiltIn.h>

#include "certs.h"
#include <WiFiClientSecure.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>
#include "WiFi.h"

#define AWS_IOT_PUBLISH_TOPIC   "env-sensor"
#define AWS_IOT_PUBLISH_HEARTBEAT_TOPIC "heartbeat"
#define AWS_IOT_PUBLISH_DOOR_TOPIC "door"
#define AWS_IOT_SUBSCRIBE_TOPIC "env-sensor-back-deck/sub"

WiFiClientSecure net = WiFiClientSecure();
PubSubClient client(net);

void messageHandler(char* topic, byte* payload, unsigned int length) {
  Serial.print("incoming: ");
  Serial.println(topic);
 
  StaticJsonDocument<200> doc;
  deserializeJson(doc, payload);
  const char* message = doc["message"];
  Serial.println(message);
}


void connectAWS() {
  WiFi.mode(WIFI_STA);
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
 
  Serial.println("Connecting to Wi-Fi");
 
  while (WiFi.status() != WL_CONNECTED){
    delay(500);
    Serial.print(".");
  }
 
  Serial.println("Connected to Wi-Fi");

  // Configure WiFiClientSecure to use the AWS IoT device credentials
  net.setCACert(AWS_CERT_CA);
  net.setCertificate(AWS_CERT_CRT);
  net.setPrivateKey(AWS_CERT_PRIVATE);
 
  // Connect to the MQTT broker on the AWS endpoint we defined earlier
  client.setServer(AWS_IOT_ENDPOINT, 8883);
 
  // Create a message handler
  client.setCallback(messageHandler);
 
  Serial.println("Connecting to AWS IOT");
 
  while (!client.connect(THINGNAME)) {
    Serial.print(".");
    Serial.print(client.state());
    delay(100);
  }
 
  Serial.println("Connected to AWS IOT");

  if (!client.connected()) {
    Serial.println("AWS IoT Timeout!");
    return;
  }
 
  client.subscribe(AWS_IOT_SUBSCRIBE_TOPIC);
 
  Serial.println("AWS IoT Connected!");
}

void publishMessage(float temperature, float pressure) {
  StaticJsonDocument<200> doc;
  doc["temperature"] = temperature;
  doc["pressure"] = pressure;
  doc["device_name"] = THINGNAME;

  char jsonBuffer[512];
  serializeJson(doc, jsonBuffer);
 
  client.publish(AWS_IOT_PUBLISH_TOPIC, jsonBuffer);
}

void publishHeartbeat() {
  StaticJsonDocument<200> doc;
  doc["heartbeat"] = 1;
  doc["device_name"] = THINGNAME;

  char jsonBuffer[512];
  serializeJson(doc, jsonBuffer);
 
  client.publish(AWS_IOT_PUBLISH_HEARTBEAT_TOPIC, jsonBuffer);
}

void publishDoorTransition(String door, String transition) {
  StaticJsonDocument<200> doc;
  doc["door"] = door;
  doc["transition"] = transition;
  doc["device_name"] = THINGNAME;

  char jsonBuffer[512];
  serializeJson(doc, jsonBuffer);
 
  client.publish(AWS_IOT_PUBLISH_DOOR_TOPIC, jsonBuffer);
}
```

You can find the full code for this, and some of my other ESP32 / home automation projects [here](https://github.com/jameselsey/home-automation)

