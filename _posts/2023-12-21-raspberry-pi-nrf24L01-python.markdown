---
author: admin
comments: false
date: 2023-12-21 18:00:00+00:00
layout: post
slug: raspberry-pi-nrf24L01-python
title: Raspberry Pi building NRF24L01 Python bindings
---

In order to fully build NRF24L01 python bindings on the Pi you will need to do this

```
sudo apt install -y build-essential python3-dev libboost-python-dev python3-pip python3-rpi.gpio libboost1.81-dev
git clone https://github.com/nRF24/RF24.git
cd RF24
make
sudo make install
cd RF24/pyRF24 && sudo python3 setup.py install
```

## Error resolution

If you get error like this

```
arm-linux-gnueabihf-g++ -shared -Wl,-O1 -Wl,-Bsymbolic-functions -g -fwrapv -O2 -marm -march=armv6zk -mtune=arm1176jzf-s -mfpu=vfp -mfloat-abi=hard -L/usr/local/lib -I/usr/local/include -DRF24_NO_INTERRUPT build/temp.linux-armv6l-cpython-311/pyRF24.o -L/usr/lib/arm-linux-gnueabihf -lrf24 -lboost_python3 -o build/lib.linux-armv6l-cpython-311/RF24.cpython-311-arm-linux-gnueabihf.so
/usr/bin/ld: cannot find -lboost_python3: No such file or directory
collect2: error: ld returned 1 exit status
error: command '/usr/bin/arm-linux-gnueabihf-g++' failed with exit code 1
```

Likely cause is that name of the linked library is misplaced. For example on my DietPi system the linked library 
should actually be called `boost_python311`. You can find out what it's actually called by running the dpkg command e.g.

```
$ dpkg -L libboost-python1.81.0 | grep libboost_pyt
/usr/lib/arm-linux-gnueabihf/libboost_python311.so.1.81.0
```

Then you need to change this line

```
    BOOST_LIB = "boost_python3"
```

to then rerun

```
    BOOST_LIB = "boost_python311"
```
