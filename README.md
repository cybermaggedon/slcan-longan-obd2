
# slcan-longan-obd2

## Summary

This is an Arduino sketch which makes a Longan Labs OBD2 CAN bus GPS
development kit into a CAN-USB adapter for Linux SocketCAN(can-utils).
The GPS part of the device is not used in any way.

This is totally forked from github.com/kahiroka/slcanuino.

## Supported hardware

The same basic board is available in two packages:
- https://docs.longan-labs.cc/1030003/

This repo is a fork of slcanuino, to change the ports and LEDs.

## Replacing the firmware

### Connecting to programmer

You can just connect to the USB port, and use Arduino IDE.

### Flashing the firmware

You can use the hex binary I provide.

### Arduino IDE

Alternatively, load this code into the Arduino IDE set to Arduino Leonardo
board, and upload.

## Using

Once programmed, the module provides slcan protocol over the serial header.
The device shows up with a serial port, probably /dev/ttyACM0.

Burn your Arduino with this and install can-utils for your linux
environment in advance.

### Deps

1. slcan (kernel module)
2. SocketCAN (http://www.pengutronix.de/software/libsocketcan/)
3. can-utils (https://github.com/linux-can/can-utils)

or just install can-utils package.

    $ sudo apt install can-utils

### Setup

    $ sudo slcan_attach -f -s6 -o /dev/ttyACM0  
    $ sudo slcand -S 1000000 ttyACM0 can0  
    $ sudo ifconfig can0 up  

then,

    $ candump can0

### Cleanup

    $ sudo ifconfig can0 down  
    $ sudo killall slcand  
