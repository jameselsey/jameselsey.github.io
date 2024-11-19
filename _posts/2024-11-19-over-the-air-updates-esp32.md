---
title: "Over the air (OTA) updates with the ESP32 are easy"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - iot
  - ota
  - esp32
---

Updating the firmware on an ESP32 over the air (OTA) sounds like a daunting task, but it's actually quite straightforward. It's particularly useful if you have devices in hard to reach locations, for example I have a [door sensor](/esp32-door-alarm/) in my shed that is plugged in about 4 meters off the ground, so I'd rather not be up on a ladder holding my laptop.

You can do all this in a few lines of code, at the bare minimum, you'll need this:

```cpp
#include <WiFi.h>      
#include <ArduinoOTA.h>

void setup() {
  Serial.begin(115200);

  // Connect to your Wi-Fi

  // Start the OTA service, so it knows to listen for updates
  ArduinoOTA.begin();

  // Your other setup code here...  
}

void loop() {
  // Handle OTA updates
  ArduinoOTA.handle();

}
```

That's it, just `ArduinoOTA.begin();` in your setup function, and `ArduinoOTA.handle();` in your loop function. The library will take care of the rest. You must always keep these two functions in your code, if you remove them, the device won't be listening for OTA updates any more so you'll have to connect it via a USB cable again.

Let's try it out, I have a simple sketch that will print `v1` to a TFT screen, and I'll deploy via Ardunio IDE whilst the device is plugged in via USB

![Initial version](/assets/post_images/2024/ota/v1.jpg)

Then I'll disconnect the device from my laptop, and plug it into a wall outlet. Then I'll update the sketch to print `v2` and deploy again, but this time I'll select the device from the network ports, and it will update over the air.

![Arduino IDE deploy via Wifi connection](/assets/post_images/2024/ota/arduinodeployviawifi.png)

When you click deploy, it will ask you for a password. This was my Wi-Fi password, not the device password, so make sure you have that handy.

Here we go, v2 is now displayed on the screen

![Version 2 displayed on a TFT](/assets/post_images/2024/ota/v2.jpg)

Next up, I'll try to do this from a github action or AWS, to see if I can deploy to multiple devices at the push of a button, that would be cool.
