# Name: Makefile
# Author: Randy Glenn <randy@surrealitylabs.com
# Copyright: 2013. All rights reserved.
# License: GPL 3.0

# This is a prototype Makefile. Modify it according to your needs.
# You should at least check the settings for
# DEVICE ....... The AVR device you compile for
# CLOCK ........ Target AVR clock rate in Hertz
# OBJECTS ...... The object files created from your source files. This list is
#                usually the same as the list of source files with suffix ".o".
# PROGRAMMER ... Options to avrdude which define the hardware you use for
#                uploading to the AVR and the interface where this hardware
#                is connected.
# FUSES ........ Parameters for avrdude to flash the fuses appropriately.

DEVICE     = attiny25
CLOCK      = 8000000
PROGRAMMER = -c usbtiny
PROGRAMMER_PI = -c linuxspi -P /dev/spidev0.0
BATCH_MODE ?= nonbatch
OBJECTS    = main.o debounce.o charlieplex.o common.o
VALENTINES-OBJECTS = valentines.o
FUSES	   = -U lfuse:w:0xe2:m -U hfuse:w:0x5f:m -U efuse:w:0xff:m

ifeq ($(BATCH_MODE),pigrammer)
	AVRDUDE = avrdude $(PROGRAMMER_PI) -p $(DEVICE) -B 10 -F
else
	AVRDUDE = avrdude $(PROGRAMMER) -p $(DEVICE) -B 10 -F
endif
COMPILE = avr-gcc -Wall -Os -DF_CPU=$(CLOCK) -mmcu=$(DEVICE)

# symbolic targets:
all:	valentines.hex	valentines-size

main.o:

.c.o:
	$(COMPILE) -c $< -o $@

.S.o:
	$(COMPILE) -x assembler-with-cpp -c $< -o $@
# "-x assembler-with-cpp" should not be necessary since this is the default
# file type for the .S (with capital S) extension. However, upper case
# characters are not always preserved on Windows. To ensure WinAVR
# compatibility define the file type manually.

.c.s:
	$(COMPILE) -S $< -o $@

valentines-flash:	valentines.hex  valentines-size
	$(AVRDUDE) -U flash:w:valentines.hex:i

fuse:
	$(AVRDUDE) $(FUSES)

valentines-size:	valentines.elf
ifneq ($(BATCH_MODE), pigrammer)
	avr-size --mcu=$(DEVICE) --format=avr valentines.elf
endif

ident:
	$(AVRDUDE)

clean:
	rm -f $(OBJECTS)
	rm -f valentines.hex valentines.elf $(VALENTINES-OBJECTS)

# file targets:
valentines.elf: $(OBJECTS) $(VALENTINES-OBJECTS)
	$(COMPILE) -o valentines.elf $(OBJECTS) $(VALENTINES-OBJECTS)

valentines.hex: valentines.elf
	rm -f valentines.hex
	avr-objcopy -j .text -j .data -O ihex valentines.elf valentines.hex
