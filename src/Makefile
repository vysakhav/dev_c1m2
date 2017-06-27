#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# This Makefile used to generate executable for HOST and MSP architecture.
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#     TARGET should be 'build' which is a phony directive which will do all
#     the build process.
#
# Platform Overrides:
#	This Makefile supports 2 platforms HOST and MSP432.
#------------------------------------------------------------------------------

#Macro for Executable.
TARGET = c1m2.out


#Check the platform based on the input command line argument.
#For the host platform.
ifeq ($(PLATFORM),HOST)
	CC = gcc
	CFLAGS = -Wall\
		 -Werror\
		 -g\
		 -std=c99
	SOURCES = main.c\
	 	  memory.c
	INCLUDE = -I../include/common
	OBJS = main.o\
	       memory.o
	PLATFORM=HOST

#Build Rule for Host Machine.
#Use all the object file to create target executable.
$(TARGET) : $(OBJS)
	$(CC) $(CFLAGS) -D$(PLATFORM) $(INCLUDE) -o $(TARGET) $(OBJS)

#Build rule for Host machine to generate obj files.
$(OBJS) : $(SOURCES)
	$(CC) $(CFLAGS) -D$(PLATFORM) $(INCLUDE)  -c $^
#For the MSP432 platform.
else
	CC =  /home/vysakhav/Mywork/Coursera/embeddedsystem/arm/gcc-arm-none-eabi-6-2017-q1-update/bin/arm-none-eabi-gcc
	CFLAGS = -Wall\
		 -Werror\
		 -g\
		 -std=c99\
		 -mcpu=cortex-m4\
		 -mthumb\
		 -march=armv7e-m\
		 -mfloat-abi=hard\
		 -mfpu=fpv4-sp-d16\
		 --specs=nosys.specs
	LINKER_FILE = ../msp432p401r.lds
	PLATFORM=MSP432
	SOURCES = main.c\
		  memory.c\
		  interrupts_msp432p401r_gcc.c\
		  system_msp432p401r.c
	INCLUDE = -I../include/common\
		  -I../include/CMSIS\
		  -I../msp432

	OBJS = main.o\
	       memory.o\
	       interrupts_msp432p401r_gcc.o\
	       system_msp432p401r.o

$(TARGET) : $(OBJS)
	$(CC) $(CFLAGS) -D$(PLATFORM) $(INCLUDE) -T$(LINKER_FILE) -o $(TARGET) $(OBJS)

$(OBJS) : $(SOURCES)
	$(CC) $(CFLAGS) -D$(PLATFORM) $(INCLUDE) -T$(LINKER_FILE)  -c $^

endif

#PHONY directive for build target.
#Execute TARGER rule implicitly.
.PHONY:
build: $(TARGET)
	

# Here is a Pattern Rule, often used for compile-line.
# It says how to create a file with a .o suffix, given a file with a .c suffix.
# The rule's command uses some built-in Make Macros:
# $@ for the pattern-matched target.
# $< for the pattern-matched dependency.
%.o : %.c
	$(CC) $(CFLAGS) -I$(INCLUDE) -D$(PLATFORM) -o $@ -c $^

#Pattern rues to generate dependencies.
#For each corresponding c file dependecny file create.
#usage eg: make main.dep PLATFOR=<platform>
#Using -M option.
#$@ Target
#$^ Prerequisits.
%.dep : %.c
	$(CC) $(CFLAGS) -I$(INCLUDE) -D$(PLATFORM) -M $^ -o $@

#Pattern rule to generate Preprocessed file.
#For each corresponding c file corresponding preprocessd file will creat.
#usage eg: make main.i PLATFOR=<platform>
#Using -E option.
#$@ Target
#$^ Prerequisits.
%.i : %.c
	$(CC) $(CFLAGS) $(INCLUDE) -D$(PLATFORM) -E $^ -o $@ 

#Pattern rule to generate Assembly file.
#For each corresponding c file asm file created.
#usage eg: make main.asm PLATFOR=<platform>
#Using -S option.
#$@ Target
#$^ Prerequisits.
%.asm : %.c
	$(CC) $(CFLAGS) $(INCLUDE) -D$(PLATFORM) -S $^ -o $@


#compile-all directive
#Usage eg: make compile-all PLATFORM=<platform>
#Create object file for each source but not link.
#Using -c option in gcc.
.PHONY:
compile-all:
	$(CC) $(CFLAGS) $(INCLUDE) -D$(PLATFORM) -c $(OBJS:.o=.c) 

#.phony target for cleanup the object files and executable.
REBUILDABLES=$(OBJS) $(TARGET)
clean:
	rm -rf $(REBUILDABLES)
	rm -rf *.i
	rm -rf *.dep
	rm -rf *.asm

