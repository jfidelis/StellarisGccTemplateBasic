TARGET = TemplateBasic

#### Setup ####
STELLARISWARE = ../../../StellarisWare
SRC           = $(wildcard src/*.c)
TOOLCHAIN     = arm-none-eabi
PART          = LM4F120H5QR
CPU           = cortex-m4
FPU           = fpv4-sp-d16
FABI          = softfp

LINKER_FILE = src/LM4F120.ld


CC = $(TOOLCHAIN)-gcc
LD = $(TOOLCHAIN)-ld
CP = $(TOOLCHAIN)-objcopy
OD = $(TOOLCHAIN)-objdump

CFLAGS = -mthumb -mcpu=$(CPU) -mfpu=$(FPU) -mfloat-abi=$(FABI)
CFLAGS+= -Os -ffunction-sections -fdata-sections
CFLAGS+= -MD -std=c99 -Wall -pedantic
CFLAGS+= -DPART_$(PART) -c -DTARGET_IS_BLIZZARD_RA1
CFLAGS+= -g
CFLAGS+= -I $(STELLARISWARE)
CFLAGS+= -I $(STELLARISWARE)/inc
CFLAGS+= -I $(STELLARISWARE)/driverlib
CFLAGS+= -I $(STELLARISWARE)/utils
SRC        += $(STELLARISWARE)/utils/uartstdio.c

LIB_GCC_PATH=$(shell $(CC) $(CFLAGS) -print-libgcc-file-name)
LIBC_PATH=$(shell $(CC) $(CFLAGS) -print-file-name=libc.a)
LIBM_PATH=$(shell $(CC) $(CFLAGS) -print-file-name=libm.a)
LIBD_PATH=$(STELLARISWARE)/driverlib/gcc-cm4f/libdriver-cm4f.a
LFLAGS = --gc-sections --entry ResetISR
CPFLAGS = -Obinary

ODFLAGS = -S

FLASHER=LMFlash
FLASHER_FLAGS=-e --hreset --reset

OBJS = $(SRC:.c=.o)

#### Rules ####
all: $(OBJS) $(TARGET).axf $(TARGET)

%.o: %.c
	@echo
	@echo Compiling $<...
	$(CC) -c $(CFLAGS) $< -o $@

$(TARGET).axf: $(OBJS)
	@echo
	@echo Linking...
	$(LD) -T $(LINKER_FILE) $(LFLAGS) -o bin/$(TARGET).axf $(OBJS) $(LIBM_PATH) $(LIBC_PATH) $(LIB_GCC_PATH) $(LIBD_PATH)

$(TARGET): $(TARGET).axf
	@echo
	@echo Copying...
	$(CP) $(CPFLAGS) bin/$(TARGET).axf bin/$(TARGET).bin
	$(OD) $(ODFLAGS) bin/$(TARGET).axf >> bin/$(TARGET).lst
	

install: $(TARGET)
	@echo
	@echo Flashing...
	$(FLASHER) bin/$(TARGET).bin $(FLASHER_FLAGS)

clean:
	@echo
	@echo Cleaning...
	rm src/*.o src/*.d bin/*
