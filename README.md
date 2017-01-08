# TheMatrix - GettingStarted

Getting started with TheMatrix from Boldport

## Bus Pirate

If you've got a [Bus Pirate](http://dangerousprototypes.com/docs/Bus_Pirate), it's a convenient way of getting started with an I2C device such as TheMatrix. Here's how to get it working.

### Connections

Hooking TheMatrix up to the Bus Pirate is straightforward - it's virtually the same as for any other I2C device. Here are the connections you'll need:

Bus Pirate|TheMatrix|Notes
----------|---------|-----
GND|GND|Ground
+3V3|VCC|Power (either VCC pin will do, they're both connected together)
VPU|VCC|Pull-up voltage (connect to the other VCC pin)
MOSI|SDA|I2C serial data
CLK|SCL|I2C serial clock
AUX|RSTn|(optional) aux line to control reset

### Bus Pirate Setup

Once it's wired up, you need to connect to the Bus Pirate over a serial console. I use `screen` on the Mac but anything at all should be fine. When you first connect to the Bus Pirate, you may need to hit return to see a first prompt (it should be `HiZ` to show you that it's unconfigured and still in high impedance mode). *The commands below are case sensitive.*

#### Mode Selection

Select I2C mode at the Bus Pirate terminal:

    m4
    4

That selects I2C mode at 400kHz.

#### I2C Setup

Enable the Bus Pirate power and pullup resistors, bring AUX high (so we're not holding TheMatrix in reset) and check the voltage status:

    WPAv

The 3.3V line should read 3.3V, as should the VPU pullup voltage. SDA and SCL should both be high, as should AUX.

Scan the I2C bus to check that TheMatrix responds:

    (1)

If you haven't installed a resistor at R4 to specify otherwise, TheMatrix should respond at address 0x30. In use, either a 0 (for writes) or a 1 (for reads) is appended so the address is effectively shifted one bit to the left. Double the scanned address for writes and then add 1 for reads. In the following examples, my controller is on address 0x30 so I'm using 0x60 for writes and 0x61 for reads.

### Setup for the Impatient

If your Bus Pirate can see TheMatrix and you just want to turn all the LEDs on to test that everything works (with a conservative 5mA LED current), you can copy and paste the following as one giant string into your Bus Pirate terminal and leave the explanation for later. :)

    [0x60 0xfd 0xc0][0x60 0x09 0x00]%:5[0x60 0xfd 0xc0][0x60 0x06 0x01][0x60 0x05 0x2A][0x60 0x04 0x2b][0x60 0xfd 0x01][0x60 0x00 0xff 0x03 0xff:22][0x60 0xfd 0x40][0x60 0x00 0x00:24 0xff:132][0x60 0xfd 0xc0][0x60 0x00 0x40][0x60 0x09 0x03]

(If you are not using the default address, you'll need to change every occurrence of `0x60` in the commands above to match your address instead.)

The four tests below rely on the test above having been run already - the Bus Pirate has a limited buffer and including the setup with each of these tests makes them too long to enter in one go!

To light alternate rows, beginning with the top:

    [0x60 0xfd 0x01][0x60 0x00 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02 0xb5 0x02]

To light alternate rows, beginning with the second from top:

    [0x60 0xfd 0x01][0x60 0x00 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01 0x4a 0x01]

To light alternate columns, starting from the first:

    [0x60 0xfd 0x01][0x60 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00 0x1f 0x00]

To light alternate columns, starting from the second:

    [0x60 0xfd 0x01][0x60 0x00 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03 0xe0 0x03]

For a quick test pattern:

    [0x60 0xfd 0x01][0x60 0x00 0x9f 0x00 0xe4 0x03 0xe0 0x03 0xb5 0x02 0x11 0x00 0x1f 0x02 0x10 0x02 0xe0 0x03 0x10 0x02 0x10 0x00 0x2e 0x02 0xd1 0x01]
