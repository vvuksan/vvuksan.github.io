---
author: admin
comments: false
date: 2015-02-04 18:00:00+00:00
layout: post
slug: adventures-with-arduino-2
title: Adventures with Arduino part 2
---

In my last post [Adventures with Arduino part 1](/2015/01/01/adventures-with-arduino-part1/) I discussed
some of the options of wiring up and getting metrics with Arduino. Here is the work in progress

[![Arduino Wiring image](/assets/arduino1.jpg)](/assets/arduino1.jpg)

It includes a [DHT11](http://www.adafruit.com/product/386) humidity temperature sensor,
[water sensor](http://www.instructables.com/id/Water-Level-Sensor-Module-for-Arduino-AVR-ARM-STM3/) and a
[reid switch](http://www.adafruit.com/product/375). The way things are set up is that it polls the sensors periodically e.g.

  - Reid switch (to see if door is open or closed) every 2-10 seconds
  - Humidity and temperature every minute

It then sends those values as a simple comma separated file. Data format I'm using is

```device uptime,device name,metric_name=value```

with multiple metric values possibly sent in the same packet. On the receiving side I have a Raspberry
Pi that follows this work flow.

  - Uses a modified raspberryfriends daemon from [nrf24pihub](https://github.com/riyas-org/nrf24pihub/)
  - Daemon receives and parses the payload and ships it off to [Statsd](https://github.com/sivy/pystatsd/) - using a gauge data type
  - Statsd rolls up any metrics and sends them over to [Ganglia](http://ganglia.info). Ganglia is used for trending
  and data collection e.g. this shows temperature and humidity in one of my bedrooms. You can notice the effect of a
  room humidifer on humidity in the room :-)
  
  
  [![Arduino metrics](/assets/arduino_humidity_temperature.png)](/assets/arduino_humidity_temperature.png)
  
  - I can also see if I left my garage door open :-)
  
  
  [![Arduino metrics](/assets/garage_door.png)](/assets/garage_door.png)

  - In addition I have set up alerting using [Nagios/Ganglia integration](https://github.com/ganglia/ganglia-web/wiki/Nagios-Integration)
  and [OpsGenie](http://www.opsgenie.com/) which alerts me if I leave my garage door open
  
  [![Arduino metrics](/assets/garage_door_alerts.png)](/assets/garage_door_alerts.png)

  - In this particular instance that alert has dual meaning since this particular Arduino is driven by one of those "lip-stick" USB battery packs
  and Ganglia will expire a particular metric if it hasn't been reported for a defined amount of time (in my case 1 minute).
  In this particular case alert state UNKNOWN tells me that most likely battery is out I need to recharge it. 

