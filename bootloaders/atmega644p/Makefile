# Makefile for ATmegaBOOT
# E.Lins, 18.7.2005
# $Id$


# program name should not be changed...
PROGRAM    = ATmegaBOOT_644P

# enter the target CPU frequency
AVR_FREQ   = 16000000L

# This part is super important here.  Depending on what you set the
# section-start parameter to, it will affect what you have to set for the
# high fuse byte.  For 0xF000, that means you have to use a bootloader
# size of 2048 words so the boot section start is:
#
#    0x7800 (word address) == 0xF000 (byte address)
#
# One word is 2 bytes for atmega chips.
# Sooo, when you program the high fuse byte, BOOTSZ will be 01 and
# BOOTRST should be enabled.
#
# I originally tried using 0xF800 or 1024 words for the boot size but
# it turns out the bootloader is too big and avrdude bombs out when
# you try to upload it.
#
MCU_TARGET = atmega644p
LDSECTION  = --section-start=.text=0xF000

OBJ        = $(PROGRAM).o
OPTIMIZE   = -O2

DEFS       = 
LIBS       = 

CC         = avr-gcc


# Override is only needed by avr-lib build system.

override CFLAGS        = -g -Wall $(OPTIMIZE) -mmcu=$(MCU_TARGET) -DF_CPU=$(AVR_FREQ) $(DEFS)
override LDFLAGS       = -Wl,$(LDSECTION)
#override LDFLAGS       = -Wl,-Map,$(PROGRAM).map,$(LDSECTION)

OBJCOPY        = avr-objcopy
OBJDUMP        = avr-objdump

all: CFLAGS += '-DMAX_TIME_COUNT=8000000L>>1' -DADABOOT
all: $(PROGRAM).hex

$(PROGRAM).hex: $(PROGRAM).elf
	$(OBJCOPY) -j .text -j .data -O ihex $< $@
	
$(PROGRAM).elf: $(OBJ)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $^ $(LIBS)
	
$(OBJ): ATmegaBOOT.c
	avr-gcc $(CFLAGS) $(LDFLAGS) -c -g -O2 -Wall -mmcu=$(MCU_TARGET) ATmegaBOOT.c -o $(PROGRAM).o

%.lst: %.elf
	$(OBJDUMP) -h -S $< > $@

%.srec: %.elf
	$(OBJCOPY) -j .text -j .data -O srec $< $@

%.bin: %.elf
	$(OBJCOPY) -j .text -j .data -O binary $< $@

clean:
	rm -rf *.o *.elf *.lst *.map *.sym *.lss *.eep *.srec *.bin *.hex
	
