# Adafruit LCD on RPi

## Enable I2C

Create file `/etc/moudles-load.d/i2c.conf` with the following content
```
i2c-dev
```

The `/etc/modules-load.d/` directory is inspected by `systemd-modules-load.service`.

Confirm that the SPE and I2C devices are present:
```
[root@alarmpi ~]# ls -l /dev/i2c-*
crw------- 1 root root 89, 1 May  3 17:42 /dev/i2c-1
crw------- 1 root root 89, 2 May  3 17:42 /dev/i2c-2
[root@alarmpi ~]#
```

## Confirm I2C is working
 1. `sudo pacman -S i2c-tools`
 3. `i2cdetect 1`
The output of `i2cdetect` above is:
```
0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: 20 -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- UU -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --                         
```

The device at address `20` should be our LCD character. UU at the 4e address indicates it is in use by a driver (most likely the Audio card).


https://learn.adafruit.com/circuitpython-on-raspberrypi-linux/installing-circuitpython-on-raspberry-pi

Install Blinka, CircuitPython and CharLCD

From what I understood, CircuitPython is Adafuit's Python library that can
fit into microcontroller, and control LCD. Blinka is a layer that allows
normal Python (from Raspberry PI) talk to the CircuitPython. This way
Adafruit's drivers can be reused, without being ported to Raspberry Pi.

 1. sudo pip3 install --upgrade adafruit-python-shell (might not be needed?)
 2. export CFLAGS=-fcommon
 2. sudo pip3 install --upgrade RPi.GPIO
 3. sudo pip3 isntall --upgrade adafruit-blinka
 4. sudo pip3 install adafruit-circuitpython-charlcd


# Alternative
https://github.com/vsergeev/python-periphery will work with I2C bus.
Port "blinka" based code to python-periphery?
