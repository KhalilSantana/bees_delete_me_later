TAG ?= $(shell git describe --always --dirty || echo UNKNOWN)

default: libcrucible.a
%.a: Makefile

CRUCIBLE_OBJS = \
	bytevector.o \
	chatter.o \
	city.o \
	cleanup.o \
	crc64.o \
	error.o \
	extentwalker.o \
	fd.o \
	fs.o \
	ntoa.o \
	path.o \
	process.o \
	string.o \
	task.o \
	time.o \
	uname.o \

include ../makeflags
-include ../localconf
include ../Defines.mk

BEES_LDFLAGS = $(LDFLAGS)

configure.h: configure.h.in
	$(TEMPLATE_COMPILER)

.depends:
	mkdir -p $@

.depends/%.dep: %.cc configure.h Makefile | .depends
	$(CXX) $(BEES_CXXFLAGS) -M -MF $@ -MT $(<:.cc=.o) $<

depends.mk: $(CRUCIBLE_OBJS:%.o=.depends/%.dep)
	cat $^ > $@.new
	mv -f $@.new $@

.version.cc: configure.h Makefile ../makeflags $(CRUCIBLE_OBJS:.o=.cc) ../include/crucible/*.h
	echo "namespace crucible { const char *VERSION = \"$(TAG)\"; }" > $@.new
	if ! cmp "$@.new" "$@"; then mv -fv $@.new $@; fi

include depends.mk

%.o: %.cc ../makeflags
	$(CXX) $(BEES_CXXFLAGS) -o $@ -c $<

libcrucible.a: $(CRUCIBLE_OBJS) .version.o
	$(AR) rcs $@ $^
