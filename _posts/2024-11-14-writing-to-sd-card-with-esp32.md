---
title: "Writing to an SD card with the ESP32"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - aws
  - iot
  - esp32
---

![](/assets/post_images/2024/sdcard/sdcardbreadboard.jpg)

## At a glance
I bought an SD card reader module to use in another project, to track GPS data, but before adding it into that project, I wanted something simple to see how I can read/write to the card.

## Hardware
The parts I used:
1. ESP32
2. SD card module from Temu
3. Jumper wires

As with most cheap electronics from the likes of Aliexpress or Temu, you don't get any instructions or guides, so here's my wiring

![](/assets/post_images/2024/sdcard/sdcardpins.jpg)

| Module Pin Number | Pin Name   | Description                                | ESP32 Pin |
|-------------------|------------|--------------------------------------------|-----------|
| 1                 | 3V3        | Power positive (+3.3V)                     | 3.3V      |
| 2                 | CS         | Chip Select (LCD selection control signal) | GPIO 21   |
| 3                 | SDI (MOSI) | SPI bus write/read data signal (MOSI)      | GPIO 23   |
| 4                 | SCK        | SPI bus clock signal                       | GPIO 18   |
| 5                 | SDO (MISO) | SPI bus write/read data signal (MISO)      | GPIO 19   |
| 6                 | GND        | Power ground                               | GND       |



## Software

Here is some simple code that just writes 10 lines of text to a file on the SD card.

One of the things I learned, is that the filename needs the leading slash, so `/example.txt` is correct, but `example.txt` will give you an error, but it wasn't obvious why.


```cpp
#include <SPI.h>
#include <SD.h>

const int chipSelect = 21; // Set to the pin you've used for CS (chip select)

void setup() {
  Serial.begin(115200);

  // Initialize the SD card
  if (!SD.begin(chipSelect)) {
    Serial.println("SD card initialization failed!");
    return;
  }
  Serial.println("SD card initialized.");

  // Open file for writing
  File myFile = SD.open("/example.txt", FILE_WRITE);
  if (myFile) {
    Serial.println("Writing to example.txt...");

    // Write 10 lines of text to the file
    for (int i = 1; i <= 10; i++) {
      myFile.print("This is line ");
      myFile.println(i);
    }

    // Close the file
    myFile.close();
    Serial.println("Done writing.");
  } else {
    // If the file didn't open, print an error:
    Serial.println("Error opening file for writing.");
  }
}

void loop() {
  // Nothing to do here
}


```

You can then remove the SD card and read the file on your computer to see the contents.

![](/assets/post_images/2024/sdcard/sdfiles.png)