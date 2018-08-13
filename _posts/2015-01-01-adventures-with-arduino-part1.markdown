---
author: admin
comments: false
date: 2015-01-01 18:00:00+00:00
layout: post
slug: adventures-with-arduino-1
title: Adventures with Arduino part 1
---

Few months ago I got myself an [Arduino Uno board from Adafruit](https://www.adafruit.com/products/50).
I have had couple use cases I was going to try to use them for in order of importance

  1. Water leak detection e.g. put a sensor in crawl space or attic for early detection of water leaks
  2. Garage door open/close state - find out if the garage door has been left open
  3. Humidity and temperature monitoring around the house


One nice thing about Arduino is that some of the small boards are really inexpensive. 
For example [Arduino Nano board](http://arduino.cc/en/Main/arduinoBoardNano) or 
[Adafruit Trinket](https://www.adafruit.com/products/1501) can run you anywhere between $3.50-$7. 

After receiving the board and playing with some of the basic examples I figured it was time to resolve
how to push sensor data to a central location. Some of the options I discovered were as follows

  1. [XBee modules](https://www.adafruit.com/product/128). Approx cost per module ~ $20
  2. Bluetooth - Approx cost per module $7
  3. 315/433 Mhz remote control - Approx cost per module ~ $1
  4. Nordic Semiconductor nRF24L01+ modules - Approx cost per module ~ $1

XBee and Bluetooth are really nice options however I considered them too overpriced for this particular
use case so I went ahead and bought a pair of 433 Mhz and nRF24L01+ modules.

## 315/433MHz modules

First thing I tried was 433 MHz modules. They were easy to wire and configure since e.g. transmitter
has only 3 pins and receiver 4 pins (although you only use 3) and using the
[rc-switch project libraries](https://code.google.com/p/rc-switch/) I was able to communicate between
my Arduino and a Raspberry Pi. The drawback is that it's fairly low bandwidth and payload size maxes
out at 24 bit so pretty limiting.

That said an interesting side benefit of these modules is that large number of remote controlled power
outlets out there use either 315/433 Mhz bands e.g. [Etekcity outlets](http://www.etekcity.com/c-59-outlets.aspx).
If you have a remote controlled device in your house you can look up what frequency it uses with 
[FCC ID search](http://transition.fcc.gov/oet/ea/fccid/). There is also the 303 Mhz frequency however
I have not been able to find the modules for it yet.

As a result of this tinkering I know am able to turn outlets around my house with my phone :-).

## Nordic Semiconductor nRF24L01+

These are a lot more tricky to get going as they have total of 8 pins with one unused and it is
easy to mis-wire things. It took me a lot of trying to get these going however I finally got it
going and was able to pass data between the Arduino and a Raspberry Pi. Max payload on this
is 32 bytes which should be enough for shipping out metric data and you can ship them at a pretty
rapid rate. Libraries I ended up using were these

[RF24 library](https://github.com/stanleyseow/RF24) and [NRF24PiHub](https://github.com/riyas-org/nrf24pihub/)

Do note that these may not work with Adafruit's Trinket.

## Next steps

I have ordered some [temperature and humidity sensors](https://learn.adafruit.com/dht),
[water sensors](http://www.instructables.com/id/Water-Level-Sensor-Module-for-Arduino-AVR-ARM-STM3/)
and some Arduino Nanos and will be wiring it up :-), shiping metrics to [Ganglia](http://ganglia.info/)
and alerting on it.