##
## Makefile for writing PROCESSES for the Miosix embedded OS
##

SRC := \
main.cpp

## Replaces both "foo.cpp"-->"foo.o" and "foo.c"-->"foo.o"
OBJ := $(addsuffix .o, $(basename $(SRC)))

AS  := arm-miosix-eabi-as
CC  := arm-miosix-eabi-gcc
CXX := arm-miosix-eabi-g++
SZ  := arm-miosix-eabi-size

AFLAGS   := -mcpu=cortex-m3 -mthumb
CFLAGS   := -mcpu=cortex-m3 -mthumb -mfix-cortex-m3-ldrd -fpie -msingle-pic-base \
            -ffunction-sections -O2 -Wall -c
CXXFLAGS := $(CFLAGS)
LFLAGS   := -mcpu=cortex-m3 -mthumb -mfix-cortex-m3-ldrd -fpie -msingle-pic-base \
            -Wl,--gc-sections,-Map,main.map,-T./miosix.ld,-n,-pie,--spare-dynamic-tags,3,--target2=mx-data-rel \
            -O2 -nostdlib
DFLAGS    := -MMD -MP

LINK_LIBS := -Wl,--start-group -lstdc++ -lc -lm -lgcc -Wl,--end-group

all: $(OBJ) crt0.o crt1.o
	$(CXX) $(LFLAGS) -o main.elf $(OBJ) crt0.o crt1.o $(LINK_LIBS)
	$(SZ)  main.elf
# 	@arm-miosix-eabi-objdump -Dslx main.elf > main.txt
	@arm-miosix-eabi-strip main.elf
	@mx-postlinker main.elf --ramsize=16384 --stacksize=2048 --strip-sectheader
	@xxd -i main.elf | sed 's/unsigned char/const unsigned char __attribute__((aligned(8)))/' > proc.h

clean:
	-rm -f $(OBJ) crt0.o crt1.o crt1.d main.elf main.map main.txt $(OBJ:.o=.d)

%.o: %.s
	$(AS) $(AFLAGS) $< -o $@

%.o : %.c
	$(CC) $(DFLAGS) $(CFLAGS) $< -o $@

%.o : %.cpp
	$(CXX) $(DFLAGS) $(CXXFLAGS) $< -o $@

#pull in dependecy info for existing .o files
-include $(OBJ:.o=.d)
