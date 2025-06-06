TOPDIR=../..
ifndef SRCDIR
  SRCDIR=$(shell pwd)
endif
-include $(TOPDIR)/tests/MakeVars

# include headers the object include directory
CPPFLAGS += -I$(TOPDIR)/include
# include headers from the compiler directory
CPPFLAGS += -I$(SRCDIR)/$(TOPDIR)/src/bin/libint
# include headers from the src/bin/test_eri directory
CPPFLAGS += -I$(SRCDIR)/$(TOPDIR)/src/bin/test_eri
# include headers from the library directory
CPPFLAGS += -I$(SRCDIR)/$(TOPDIR)/src/lib/libint
# include processed headers from the compiler directory
CPPFLAGS += -I$(TOPDIR)/src/bin/libint

OBJSUF = o
DEPSUF = d
CXXDEPENDSUF = none
CXXDEPENDFLAGS = -M

TEST = test
CXXTESTSRC = $(TEST).cc
CXXTESTOBJ = $(CXXTESTSRC:%.cc=%.$(OBJSUF))
CXXTESTDEP = $(CXXTESTSRC:%.cc=%.$(DEPSUF))

check:: $(TEST)
	LD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(LD_LIBRARY_PATH) DYLD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(DYLD_LIBRARY_PATH) ./$(TEST) 0 2
	LD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(LD_LIBRARY_PATH) DYLD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(DYLD_LIBRARY_PATH) ./$(TEST) 1 1
	LD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(LD_LIBRARY_PATH) DYLD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(DYLD_LIBRARY_PATH) ./$(TEST) 2 1
	LD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(LD_LIBRARY_PATH) DYLD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(DYLD_LIBRARY_PATH) ./$(TEST) 3 1
	LD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(LD_LIBRARY_PATH) DYLD_LIBRARY_PATH=$(TOPDIR)/lib/.libs:$(DYLD_LIBRARY_PATH) ./$(TEST) 4 1

ifeq ($(CXX_COMPATIBLE_WITH_CXXGEN),yes)
$(TEST): $(CXXTESTOBJ) $(COMPILER_LIB) $(COMPUTE_LIB)
	$(LD) -o $@ $(CXXFLAGS) $(LDFLAGS) $^ $(SYSLIBS)
else
$(TEST):
	echo "Cannot test generated code! $(CXXGEN) is not compatible with $(CXXCOMP)"
	@exit 0
endif

# Source files for timer and tester are to be compiled using CXXGEN
$(TEST): CXX=$(CXXGEN)
$(TEST): CXXFLAGS=$(CXXGENFLAGS)
$(TEST): LD=$(CXXGEN)

clean::
	-rm -rf $(TEST) *.o *.d

distclean:: realclean

realclean:: clean

targetclean:: clean

depend:: $(CXXTESTDEP)

ifneq ($(DODEPEND),no)
ifneq ($(CXXDEPENDSUF),none)
%.d: %.cc
	$(CXXDEPEND) $(CXXDEPENDFLAGS) -c $(CPPFLAGS) $(CXXFLAGS) $< > /dev/null
	sed 's/^$*.o/$*.$(OBJSUF) $*.d/g' < $(*F).$(CXXDEPENDSUF) > $(@F)
	/bin/rm -f $(*F).$(CXXDEPENDSUF)
else
%.d: %.cc
	$(CXXDEPEND) $(CXXDEPENDFLAGS) -c $(CPPFLAGS) $(CXXFLAGS) $< | sed 's/^$*.o/$*.$(OBJSUF) $*.d/g' > $(@F)
endif

-include $(CXXTESTDEP)
endif
