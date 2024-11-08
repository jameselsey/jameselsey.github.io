---
title: "Getting started with a TFT display on an ESP32"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - aws
  - iot
  - esp32
---

![](/assets/post_images/2024/tftdisplay.jpg)

## At a glance
I bought a small TFT display from Temu to be used in a project, but I wanted to test it out first. The display is a 1.8" TFT with a resolution of 128x160 pixels, it's a cheap display that's easy to use with the ESP32.

## Hardware
The parts I used:
1. ESP32
2. 1.8" TFT display from Temu
3. Jumper wires

As with most cheap electronics from the likes of Aliexpress or Temu, you don't get any instructions or guides, so here's my wiring

![](/assets/post_images/2024/tftpins.png)

| TFT Pin Number | Pin Name   | Description                                | ESP32 Pin |
|----------------|------------|--------------------------------------------|-----------|
| 1              | VCC        | Power positive (+3.3V)                     | 3.3V      |
| 2              | GND        | Power ground                               | GND       |
| 3              | CS         | Chip Select (LCD selection control signal) | GPIO 5    |
| 4              | RESET      | LCD reset control signal                   | GPIO 4    |
| 5              | DC         | LCD command/data selection signal          | GPIO 2    |
| 6              | SDI (MOSI) | SPI bus write/read data signal (MOSI)      | GPIO 23   |
| 7              | SCK        | SPI bus clock signal                       | GPIO 18   |
| 8              | LED        | LCD backlight control signal               | 3.3V      |
| 9              | SDO (MISO) | Not connected (NC, as per spec)            | (NC)      |

![](/assets/post_images/2024/tftwiring.jpg)

## Software

Starting out, I just wanted to display some static text on the screen to see how it looked, here's the code I used. Notice how you treat the screen like a board with coordinates, setting the cursor at locations then printing output. Coordinates `0,0` denotes the top left of the display.

Libraries
* https://github.com/adafruit/Adafruit-GFX-Library
* https://github.com/adafruit/Adafruit-ST7735-Library

```cpp
#include <Adafruit_GFX.h>        // Core graphics library
#include <Adafruit_ST7735.h>     // Library for ST7735
#include <SPI.h>

// Define pins for the display
#define TFT_CS         5  // Chip Select
#define TFT_RST        4  // Reset
#define TFT_DC         2  // Data/Command

// Initialize the ST7735 display
Adafruit_ST7735 tft = Adafruit_ST7735(TFT_CS, TFT_DC, TFT_RST);

void setup() {
  // Initialize the Serial Monitor
  Serial.begin(115200);
  
  // Initialize the display
  tft.initR(INITR_BLACKTAB);  // Init ST7735S chip
  tft.setRotation(1);         // Set the display rotation
  
  // Set the display background color to black
  tft.fillScreen(ST77XX_BLACK);
  
  // Print a message on the display
  tft.setTextColor(ST77XX_WHITE);  // Set text color to white
  tft.setTextSize(1);              // Set text size
  tft.setCursor(0, 0);             // Set starting point for text
  tft.println("Hello, ESP32!");
  
  // Simulate GPS Data on the display
  float lat = 37.7749;
  float lon = -122.4194;
  float altitude = 30.0;
  
  // Print data
  tft.setCursor(0, 20);            // Move cursor down
  tft.println("GPS Data:");
  tft.print("Lat: ");
  tft.println(lat, 6);
  tft.print("Lon: ");
  tft.println(lon, 6);
  tft.print("Alt: ");
  tft.print(altitude);
  tft.println(" m");
  Serial.println("started up!");
}

void loop() {
  // You can update the display data here
}

```