FV1 EEProg
==========

Expansion board used on conjuntion with an EZ-USB FX2LP (LCSOFT version) Cypress CY7C68013A dev board (source: ebay)
to create an EEPROM programmer that works directly within the SpinAsm software. Idea based on FV-1 Dev board.

*** DOES NOT INVOLVE COPYING THE ORIGINAL FIRMWARE ***

The trick is to force a new hardware to be enumerated as FV-1 Dev board, after being detected by the SpinAsm software the firmware is uploaded via USB into the RAM of the CY7C68013A microcontroller.

The EZ-USB FX2LP board requires a small modification using an external I2C EEPROM programmer, like i.e. PicKit2:
The onboars 24LC128 EEPROM needs to be erased and written with te following sequence of bytes starting from the address 0x00:

C0 79 18 01 10 00 00 00

If you don't have any other EEPROM programmer the same task could be done using the Cypress CyConsole program (part of the Cypress Suite USC 3.4.7).

The jumper J1 should be installed, setting the EEPROM I2C address to 0xA2.

Here is how it works:
afer plugging the usb cable, the cypress chip looks at the I2C bus for an EEPROM at address 0xA0 to read the VID and PID and send them back to the PC.
Normally, if the programmer is connected to any FV1 based device, it would read the first bytes of the EEPROM storing the dsp program (it's address is 0xA0).
That's why the I2C bus has to be disconnected from the target after plugging the programmer into the USB. That is the job for the daughter board, consisting of a 4066
quad switch controlled from the PA0 line. It's turned on only after a succesfull enumeration opening up the I2C bus to the target.
If the MCU doesn't find any EEPROM at 0xA0 it looks if there is anything at 0xA2 and finds the onboard 24LC128 EEPROM and stored VID&PID there.

After the sucesfull enumeration and detection the RED led goes on showing the programmer is online and ready to work.
SpinAsm can directly upload the new firmware into the target eeprom and reset the dsp if necessary.

Automatic resetting the DSP after firmware upload:
this feature is done by quckly changing the T0 line (internal/external set of programs), which results in rereading the current DSP program.
In order to make a use of it in your application do not tie up the T0 line directly to the VDD (3.3V), but use a 10k pull up resistor. 

Here is the link to the OSH Park project:

https://www.oshpark.com/shared_projects/okfY0ITb

