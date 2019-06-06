# MCP23008 Button input Board also known as the KEEYPRO Dev Board

KEEYPRO is a daisychain-enabled hardware input button array that can be surface mounted to almost any device. 

It's designed to allow for the mapping of complex HID inputs from devices such as keyboards, or mice to single input buttons. 
Complex actions and inputs can be prerecorded to be executed over simulated mouse+keyboard input on the target device. 
Multiple KEEYPRO devices can be daisychained together for larger button arrays. 
It is controlled by the MCP23008E/P I2C I/O Expander. 

It supports configurable 3 bit i2c addresses and allows upto 8 devices on the i2c bus.

The datasheet for the IC can be found [on the Microchip website](https://ww1.microchip.com/downloads/en/DeviceDoc/21919e.pdf)

## Pictures

### The IC
![MCP23008](https://i.imgur.com/OzYLuiu.png)

### The Schematic
![MCP23008 Schematic](https://i.imgur.com/MhGq0gL.png)

### Some photos of assembled board
![Crappy Phone Camera Pics](https://i.imgur.com/N8MhkFw.jpg)
*nb: Adding the Resistor Network was a small oversight and is not present in this pic*


## I/O
The board has 2 JST-XH4 headers on it to allow further devices to be daisy-chained. 

Either header can be used for input and output.

The board is designed to take 5V however it should support 1.8V to 5.5V, but this has not been tested.

The header pinout is as follows:

    ------
    | +5V|
     |SCL|
     |SDA|
    | Gnd|
    ------


## Buttons
The button layout is arranged

    5   4
    6   3
    7   2
    8   1

All 8 buttons can be read at once in a single byte and upto 8 boards can be read sequencially. The buttons are pulled down to ground via a 10k Resistor Network and pull high when pressed, do not configure the internal pull-ups.


## I2C addresses
The dip switches are labelled in the opposite order to the address lines because they are.

Switch 3 = lowest settable bit
Switch 1 = highest settable bit

`0b0100[1][2][3][r/w]`

Configuring the address lines while the device is powered is undefined behavour, so probably don't do that.

Some developement boards require 10k pull-up resistors on the i2c data lines as they are not on the board, the Arduino Uno does, the Arduino Mega does not. Check your developement boards documentation to see if you need this.

## Interrupts
The interrupt pin has not been wired, but if you want to add it yourself, it is Pin 8 on the IC. Details for how to use it are listed in the datasheet.

The Adafruit MCP23008 Library does not implement the interrupt, so you'll need to create this yourself

## Software interface

There is an Adafruit Arduino library but it is pretty bearbones and has not been updated in a number of years.
The GitHub repo for the library can be found [here.](https://github.com/adafruit/Adafruit-MCP23008-library)

Some sample code is as follows:

```c
#### `mcp23008.ino`
#include <Wire.h>
#include "Adafruit_MCP23008.h"

Adafruit_MCP23008 mcp1;
Adafruit_MCP23008 mcp2;
  
void setup() {  
  mcp1.begin();      // use default address 0
  mcp2.begin(1);
  Serial.begin(9600);
  Serial.println("Hi");

  for (int i=0; i<8; i++) {
    mcp1.pinMode(i, INPUT);
    mcp2.pinMode(i, INPUT);
  }
}
void loop() {
  Serial.print("Board 1: ");
  for (int i=0; i<8; i++) {
    Serial.print(mcp1.digitalRead(i));
  }
  Serial.println();
  
  Serial.print("Board 2: ");
  for (int i=0; i<8; i++) {
    Serial.print(mcp2.digitalRead(i));
  }
  Serial.println();
}
```

This will enumerate through each pin alternating between two boards and output the results into the serial console. The boards can be read a byte at a time, but the library does not implement this.

## BOM
* 1x 5x7CM Perfboard with 0.1" pin spacing
* 1x MCP23008E/P
* 8x Buttons
* 2x 100nF Ceramic Capacitors
* 1x 9 pin common ground 10k Resistor Network
* 1x 4 pin common ground 10k Resistor Network (3x 10k Metal Film Resistors were used here)
* 2x JST-XH 4 pin male headers
* 1x 6 pin SPST DIP Switch
* ?x bits of wire

Developed by David Wilkins and Jack Darcy.
