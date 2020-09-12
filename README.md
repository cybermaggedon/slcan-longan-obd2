
# slcanserial

This is an Arduino sketch which makes a Longan Labs CAN Serial device into
a CAN-USB adapter for Linux SocketCAN(can-utils).

This is totally forked from slcanuino.

# Supported hardware

https://docs.longan-labs.cc/1030001/
https://docs.longan-labs.cc/1030002/

The Longan Serial CAN Bus Module is the target, specifically the v1.2 board.
This is a nice device, it has a programmable ATmega168P device.  It's small,
the CAN bus H/L ports are a 2-wire header, so you can wire it straight into
the bus if you can find a good place.  It sells for around $15 at the moment.

The pre-loaded firmware is a little disappointing, limits the onward
serial output to 115.2k, and has a protocol which is awkward to work with.
The bizarre thing is, this is an Arduino compatible device, so
slcanuino is a perfect firmware.  It can push data over the serial at
1000000 and integrates with SocketCAN.

This firmware is a minor port from slcanuino, to change the ports and LEDs.

# Replacing the firmware

You need an FTDI-type programmer.  You can use the hex binary I provide...

```
  avrdude -b 19200 -D -p atmega168 -c arduino -P /dev/ttyUSB0 \
    -U flash:w:slcanserial.arduino.hex
```

Alternatively, following the instructions here if you want to flash from
the Arduino IDE: https://docs.longan-labs.cc/1030001/

# Using

Once programmed, the module provides slcan protocol over the serial header.
You need to connect this with Linux somehow, easiest way is to leave the
FTDI appliance connected, and communicate with that.  It probably shows up
as /dev/ttyUSB1.

Burn your Arduino with this and install can-utils for your linux
environment in advance.

## Deps
1. slcan (kernel module)
2. SocketCAN (http://www.pengutronix.de/software/libsocketcan/)
3. can-utils (https://github.com/linux-can/can-utils)

or just install can-utils package.

    $ sudo apt install can-utils

## Setup

    $ sudo slcan_attach -f -s6 -o /dev/ttyUSB0  
    $ sudo slcand -S 1000000 ttyUSB0 can0  
    $ sudo ifconfig can0 up  

then,

    $ candump can0

## Cleanup

    $ sudo ifconfig can0 down  
    $ sudo killall slcand  
