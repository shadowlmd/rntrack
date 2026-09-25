#
# Makefile for building RNTrack with MinGW
#
# Build options:
# ENABLE_SCRIPTS=1
#    Build with embedded Perl
# ENABLE_SYSLOG_LOG_FORMAT=1
#    Write log file lines like syslog(3)
# ENABLE_LOG_PID=1
#    Write the Process ID number into log file lines.
# CONFIG=c:\\\\ftn\\\\rntrack.cfg
#    Alternate config file location
# DEBUG=1
#    Build a debug version of the executable
#
# If the shell is MSYS sh, set TERM=msys before running make.
# For cross-compiling, override the compilers, for example:
#    make CC=i686-w64-mingw32-g++ smapi_CC=i686-w64-mingw32-gcc

DEBUG ?= 0
ENABLE_SCRIPTS ?= 0
ENABLE_LOG_PID ?= 0
ENABLE_SYSLOG_LOG_FORMAT ?= 0

ROOTDIR := ../..
HPPDIR := $(ROOTDIR)/hpp
SRCDIR := $(ROOTDIR)/src
SMAPIDIR := $(ROOTDIR)/smapi
smapi_HDIR := $(SMAPIDIR)/h
smapi_SRCDIR := $(SMAPIDIR)/src

CC := g++

ifeq ($(DEBUG),0)
    CFLAGS := -O3 -Wall -fno-rtti -I$(HPPDIR) -I$(SRCDIR) -I$(smapi_HDIR) -I$(smapi_SRCDIR)
    LFLAGS := -s
else
    CFLAGS := -g -Og -Wall -fno-rtti -I$(HPPDIR) -I$(SRCDIR) -I$(smapi_HDIR) -I$(smapi_SRCDIR)
    LFLAGS :=
endif

# Link the GCC runtime statically, so the executable does not need
# libgcc_s_dw2-1.dll and libstdc++-6.dll
LFLAGS += -static-libgcc -static-libstdc++

CDEFS := -D__NT__ -DNDEBUG -DWIN32 -DWIN32IOP_H -DHAVE_IO_H \
         -DHAVE_TIME_H -DHAVE_UTIME_H -DHAVE_LOCALE_H

EXEEND := _w32.exe

ifeq ($(TERM),msys)
    RM := rm -f
else
    RM := del /Q
endif

ifeq ($(ENABLE_SCRIPTS), 1)
    CFLAGS += $(shell perl -MExtUtils::Embed -MConfig -e '@v=split(/\./,"$$]"); print "-D__PERL_VERSION__=".join("",@v)." -D__PERL_MAJOR__=$$v[0] -D__PERL_MINOR__=$$v[1] $$Config{ccdlflags} ", join(" ",grep(!/-fstack-protector/,split(" ",$$Config{ccflags}))), " -I$$Config{archlibexp}/CORE";' )
    LFLAGS += $(shell perl -MExtUtils::Embed -e ldopts )
endif

ifeq ($(ENABLE_SYSLOG_LOG_FORMAT), 1)
    CFLAGS += -DSYSLOG_LOG_FORMAT
endif

ifeq ($(ENABLE_LOG_PID), 1)
    CFLAGS += -DLOG_SHOW_PID
endif

ifdef CONFIG
    CDEFS += -DDefaultConfig=\"$(CONFIG)\"
endif

OBJS= \
	age.o \
	aix_conv.o \
	aka.o \
	attach.o \
	badmsg.o \
	badpkt.o \
	cfg.o \
	configure.o \
	domain.o \
	fidoaddr.o \
	filebox.o \
	getopt.o \
	help.o \
	log.o \
	mask.o \
	msg.o \
	msgbase.o \
	nodelist.o \
	outbound.o \
	parsetpl.o \
	passwd.o \
	pktbase.o \
	rntrack.o \
	scandir.o \
	script.o \
	sqbase.o \
	string.o \
	tmstamp.o \
	utils.o \
	vars.o \
	wildmat.o

.PHONY: all
all: rntrack$(EXEEND) ;

include $(SMAPIDIR)/makefile.mgw

rntrack$(EXEEND): $(smapi_OBJS) $(OBJS)
	$(CC) -o rntrack$(EXEEND) $(smapi_OBJS) $(OBJS) $(LFLAGS)

$(OBJS): %.o: $(SRCDIR)/%.cpp
	$(CC) $(CFLAGS) $(CDEFS) -c $(SRCDIR)/$*.cpp

.PHONY: clean distclean
clean:
	-$(RM) *.o

distclean: clean
	-$(RM) rntrack$(EXEEND)
