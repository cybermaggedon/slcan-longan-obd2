
# slcanserial

## Summary

This is an Arduino sketch which makes a Longan Labs Serial CAN device into
a CAN-USB adapter for Linux SocketCAN(can-utils).

The motivation for this is that SocketCAN/slcan provide a very usable way to
interact with devices that support the right API, whereas the firmware
supplied with this device is very limited.

You need an FTDI-type programmer to be able to program this device.  Once
programmed, you need a way to get the device to interface with a Linux system
e.g.
- The FTDI-type you used to program the device makes it like it is
  on a serial port, so you can just use that.
- If you have a Linux device with 5V TTL GPIO (e.g. BeagleBoard)
  you should be able to just hook the device up to those ports, although
  I haven't tried this.
- You can use 5V/3.3V matching circuitry to match the 5V device to
  Raspberry Pi's 3.3V GPIO and then connect to a Pi.  I haven't tried this.

This is totally forked from github.com/kahiroka/slcanuino.

## Supported hardware

The same basic board is available in two packages:
- https://docs.longan-labs.cc/1030001/
- https://docs.longan-labs.cc/1030002/

The Longan Serial CAN Bus Module is the target, specifically the v1.2 board.
I haven't tried v1.0 or v1.1, and it looks like they don't have programming
ports.

This is a nice device, it has a programmable ATmega168P device.  It's very
small, which is useful if you want to hide it away in a place where a CAN
Shield or OBD-II port would be too big.  The device has a 2-wire header, so
you can use an OBD-II interface, or just tap straight into CAN twisted pair.
It sells for around $15 at the moment.

The pre-loaded firmware is a little disappointing, limits the onward serial
output to 115.2k, and has a protocol which is awkward to work with.  The AT
commands must be submitted in 10 milliseconds, so you can't play with the
device over minicom (unless you can type VERY fast).
The bizarre thing is, this is an Arduino compatible device, so slcanuino is
a perfect firmware.  It can push data over the serial at 1000000 and
integrates with SocketCAN.

This repo is a fork of slcanuino, to change the ports and LEDs.

## Replacing the firmware

### Connecting to programmer

You need an FTDI-type programmer
e.g. https://www.amazon.co.uk/gp/product/B07R17BMTL/ .
Make sure you have one with the DTR line,
so you connect VCC, TX, RX, GND, DTR lines between FTDI programmer and the
Serial CAN device.  TX connects to TX and RX to RX, these connections don't
get crossed as you might do with other serial interconnects.

### Flashing the firmware

You can use the hex binary I provide...

```
  avrdude -b 19200 -D -p atmega168 -c arduino -P /dev/ttyUSB0 \
    -U flash:w:slcanserial.arduino.hex
```

### Arduino IDE

Alternatively, load this code into the Arduino IDE and follow the
instructions here if you want to flash from
the Arduino IDE: https://docs.longan-labs.cc/1030001/

## Using

Once programmed, the module provides slcan protocol over the serial header.
You need to connect this with Linux somehow, easiest way is to leave the
FTDI appliance connected, and communicate with that.  It probably shows up
as /dev/ttyUSB1.

Burn your Arduino with this and install can-utils for your linux
environment in advance.

### Deps

1. slcan (kernel module)
2. SocketCAN (http://www.pengutronix.de/software/libsocketcan/)
3. can-utils (https://github.com/linux-can/can-utils)

or just install can-utils package.

    $ sudo apt install can-utils

### Setup

    $ sudo slcan_attach -f -s6 -o /dev/ttyUSB0  
    $ sudo slcand -S 1000000 ttyUSB0 can0  
    $ sudo ifconfig can0 up  

then,

    $ candump can0

### Cleanup

    $ sudo ifconfig can0 down  
    $ sudo killall slcand  
