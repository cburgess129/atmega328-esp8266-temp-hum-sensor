# atmega328-esp8266-temp-hum-sensor
Sketch and connectivity for building a wireless temp/humidity sensor based on the atmega328 and esp-8266

/* This sketch uses a bare Atmega328 microcontroller, along with an ESP8266 ESP-01 wifi module
and a DHT22 temperature sensor to create a small, efficient, remote temperature and humidity
sensor.
You will need to make sure your ESP8266 is running the latest version of AT firmware.
It should be configured for a UART speed of 19200, 1 stop bit, no parity, no flow control.
It should be configured for CWMODE 1 mode.  It should be joined to your wifi and have
an IP address. There is a check to make sure the wifi is joined after the ESP is reset.  If
wifi is not joined, the ESP will attempt to rejoin the wifi SSID defined in the sketch

Pin reference:  http://www.akafugu.jp/images/microcontroller-reference-sheet.png

Pin connections:
Module/Pin     ATMEGA Arduino Pin (Dxx)   Atmega Physical Pin (xx)
----------   -  ---------------           -----------------------
error LED+      13                         19 - connect through 220ohm resistor
green LED+      12                         18 - connect through 220ohm resistor
DHT22 data      9                          15 - connect 1Kohm resistor between data pin and VCC pin
DHT22 VCC       4                          6
ESP RESET       2                          4  - connect through 1Kohm resistor                        
ESP CH_PD       3                          5  - connect through 1Kohm resistor                       
ESP TX          10 (software serial RX)    16    
ESP RX          11 (software serial TX)    17   
DEBUG RX        1  (Serial TX)             3  - connect RX pin of usb-serial adapter here to view debug data                                  
DEBUG TX        0  (Serial RX)             2 

RESET button  RESET                        1  - Connect a 10K resistor between the Atmega reset pin and 3.3v.  Connect one side of
                                                a pushbutton to the reset pin, and connect the other side of the pushbutton to GND.  When you push
                                                the button, the Atmega will reset.

FLASHING ESP FIRMWARE
You will need to add a 3-pin header to your breadboard to connect your usb-serial cable to the
tx/rx of the ESP to flash the latest firmware

  ESP Pin    USB-Serial Adapter
  -------    ------------------
  TX         RX
  RX         TX
  GND        GND
  GIPO0      GND - connect through pushbutton switch

You will need to bring ESP pin GPIO0 to ground during startup in order to put the ESP into flash mode.
I use a hard-wired switch to do this

BURNING THE BOOTLOADER ON THE ATMEGA328
You will need to add a 6-pin ICSP header to your breadboard in order to flash the bootloader to your 
Atmega chip.  You can find the ICSP pin layout on the net.  You can use either an AVR programmer or 
ArduinISP to flash the bootloader.  There are examples on the net showing you how to use an arduino 
as an  AVR-ISP programer if you don't have an AVR programmer

DEFINING THE ATMEGA328@8MHZ BOARD IN THE IDE
You need to add the following lines to your 'boards.txt' file in order to be able to flash the new boot
loader to your atmega chip and set the fuses correctly.  Just add them as a new entry, save the file, and 
restart the IDE. You will see a new board entry of '8MHZ ARDUINO UNO'.  Select this board, select the COM 
port for your AVR programmer and hit 'burn bootloader.  This entry will also allow you to upload sketches
via serial using 'UPLOAD' in the IDE - just select the COM port you usb-serial adapter is on

###########################################################
8mhzuno.name=8MHZ Arduino Uno
8mhzuno.upload.tool=avrdude
8mhzuno.upload.protocol=arduino
8mhzuno.bootloader.tool=avrdude
8mhzuno.bootloader.low_fuses=0xE2
8mhzuno.bootloader.high_fuses=0xD2
8mhzuno.bootloader.extended_fuses=0x05
8mhzuno.bootloader.unlock_bits=0x3F
8mhzuno.bootloader.lock_bits=0x0F
8mhzuno.upload.maximum_size=30720
8mhzuno.upload.maximum_data_size=2048
8mhzuno.upload.speed=57600
8mhzuno.bootloader.file=atmega/ATmegaBOOT_168_atmega328_pro_8MHz.hex
8mhzuno.build.mcu=atmega328p
8mhzuno.build.f_cpu=8000000L
8mhzuno.build.core=arduino
8mhzuno.build.variant=standard
############################################################
 
If you are using an AVR programmer, you might need to add it to the 'programmers' file in the arduino
configuration.

UPLOADING SKETCHES
Once you have updated the firmware on your ESP and flashed the bootloader on the atmega chip, you can connect
your usb-serial converter to the serial port on the Atmega:

USB-SERIAL PIN    ATMEGA Arduino PIN:
--------------    ----------
TX                0 (RX)
RX                1 (TX)
DTR               RESET - connect this through a .1uf ceramic capacitor
GND               GND

This will allow you to upload sketches by selecting the COM port of your usb-serial converter like you would
upload to a regular arduino.  If your usb-serial converter doesn't have a DTR pin, you will have to push the reset
button on your atmega before you hit the upload button in the IDE but the timing is hard to get right.  Best to 
spend $2 and get a usb-serial converter with a DTR pin on ebay

If you open this COM port in the IDE (make sure to set the BAUD to 19200 in the IDE) or connect to it with a 
terminal emulator like terraterm or putty, you can see DEBUG info from the atmega telling you if each step 
succeded or failed

POWER:  
This circuit is designed to use 3.3v.  You can power it directly from 3 AA batteries, a 3.7V LIPO
battery, or any combination of batteries/external power that is 2.7 to 4.5 volts.  The ESP module requires
a lot of current and can't be powered by a laptop USB port and can't be powered by the 3.3v LDO on a standard
aarduino
*/
