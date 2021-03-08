---
title: 'BME280 BMP280 on Wemos Lolin32 with Mongoose OS'
date: '2018-02-03T18:43:42+10:00'
permalink: /bme280-bmp280-on-wemos-lolin32-with-mongoose-os
author: 'James Elsey'
category:
    - IoT
tag:
    - aws
    - aws-iot
    - esp32
    - iot
    - wemos
---
The weekend IoT warrior is back again!

I bought a pack of 5 BME280s from ebay, unfortunately they sent BMP280s instead and wouldn’t offer a refund. A colleague at work had the same issue, it’s actually really hard to get the BME and not get fobbed off with the BMP. Another colleague mentioned to buy them from this seller on [Aliexpress](https://www.aliexpress.com/store/product/High-Accuracy-BME280-Digital-Sensor-Temperature-Humidity-Barometric-Pressure-Sensor-Module-GY-BME280-I2C-SPI-1/1414081_32672210336.html?spm=2114.12010615.0.0.4c2fd1742XLarA) as they’re legit (he had a pack of them in his hand, the right ones!). Anyway, I haven’t ordered them yet so I’ll play with the BMP280 for now, same interface, just lacks the humidity sensor.

![IMG_20180203_183636-1024x768.jpg](/assets/post_images/2018/IMG_20180203_183636.jpg)

I ordered the BME280s but got sent the BMP280s, annoying!

Finding the I2C pins
--------------------

Every board has the potential to use different SDA/SCL pins, fortunately for me, it was printed on the reverse of the board, but you might have to hunt around in the specification sheet of your board to find them.

![IMG_20180203_184213-768x1024.jpg](/assets/post_images/2018/IMG_20180203_184213.jpg)

Wemos Lolin32 board

Finding the I2C device
----------------------

You can have many devices on the I2C bus, think of it like a public highway with many cars (devices) travelling on it at any time. Each device will have it’s own ID which you’ll need to know to communicate with it in your application. If the manufacturer doesn’t specify the device ID, fortunately there is an easy way to find it using the I2C scanner that Arduino provide, [run this sketch](https://playground.arduino.cc/Main/I2cScanner) and you should see something like the following

![Screen-Shot-2018-02-03-at-6.52.11-pm-1024x871.png](/assets/post_images/2018/Screen-Shot-2018-02-03-at-6.52.11-pm.png)Arduino code for the I2C Scanner

Note that I had to override the I2C pins on the Wire.begin(), you might need to do that too.

```
Scanning...
I2C device found at address 0x76  !
done
```

Writing some code to read data from the sensor
----------------------------------------------

Now I know the I2C pins, and the device address, I can start to write some code. I started off by looking at the [example from Mongoose OS](https://github.com/mongoose-os-apps/example-arduino-adafruit-bme280-c), but it seems theres an issue with the Arduino compat library, fortunately theres an [answer on the forums](https://forum.mongoose-os.com/discussion/1977/cant-get-bme280-example-app-to-work#latest).

Here’s where I ended up

```cpp
#include <mgos.h>
#include <Adafruit_BME280.h>

#define SENSOR_ADDR 0x76

static Adafruit_BME280 *s_bme = nullptr;

// Couldn't get bme280 example app to work due to arduino compat
// here's a modified version 
// Credit to nliviu from MOS Forums for help with this 
// https://forum.mongoose-os.com/discussion/1977/cant-get-bme280-example-app-to-work#latest

void readTimerCB(void *arg)
{    
    printf("Temperature: %.2f *C\n", s_bme->readTemperature());
    // Humidity will only work on BME280, not BMP280
    printf("Humidity: %.2f %%RH\n", s_bme->readHumidity());
    printf("Pressure: %.2f kPa\n\n", s_bme->readPressure() / 1000.0);
    (void) arg;
}

enum mgos_app_init_result mgos_app_init(void)
{
    s_bme = new Adafruit_BME280();

    // Initialize sensor
    if (!s_bme->begin(SENSOR_ADDR)) {
        printf("Can't find a sensor\n");
        return MGOS_APP_INIT_ERROR;
    }

    mgos_set_timer(2000, 1, readTimerCB, NULL);

    return MGOS_APP_INIT_SUCCESS;
}
```

Grab the full source [here](https://github.com/jameselsey/mongoose-os-apps-temp-humid-sensor)

Conclusion
----------

Once again, Mongoose OS makes it REALLY easy to get started, the hardest part was figuring out the I2C connections but there’s plenty of resources online for helping with that.

My plan is to have a few of these setup, 2 inside my lizard enclosure (for hot and cool sides), a few around the house (humidity would be great, had a few issues with mould) and one for outside temperature readings. The next challenge I have is understanding how I can deploy the same application to multiple devices but with different config, so I can name the MQTT topics for each board. Time to get reading!