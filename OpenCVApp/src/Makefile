TOP = ../..
include $(TOP)/configure/CONFIG

OPENCV_SRC=$(TOP)/OpenCV-src
# extract visual studio major version number from path
# unfortunately cannot use %.% so using %.0 but may not match minor version number changes e.g. 10.1
ifneq ($(VisualStudioVersion),)
VS_VERS=$(filter %.0,$(subst \, ,$(VisualStudioVersion)))
else
VS_VERS=$(filter %.0,$(subst \, ,$(VSINSTALLDIR)))
endif
VS_MAJOR=$(word 1,$(subst ., ,$(VS_VERS)))

CMAKE ?= cmake

OPENCV_INSTALL=$(abspath $(TOP))/install/$(EPICS_HOST_ARCH)
#CMAKE_CONFIG_FLAGS=-DBOOST_ROOT:STRING=$(EPICS_ROOT)/libraries/master/boost -DGSL_INSTALL_MULTI_CONFIG=ON
CMAKE_BUILD_FLAGS=
ifneq ($(findstring linux,$(EPICS_HOST_ARCH)),)
CMAKE_GENERATOR=Unix Makefiles
else
ifneq ($(findstring windows,$(EPICS_HOST_ARCH)),)
VS_ARCH=$(VS_MAJOR)
PLATFORM=x64
else
VS_ARCH=$(VS_MAJOR)
PLATFORM=Win32
endif
CMAKE_GENERATOR=Visual Studio $(VS_ARCH)
ifeq ($(findstring static,$(EPICS_HOST_ARCH)),)
CMAKE_CONFIG_FLAGS += -DBUILD_SHARED_LIBS=ON
WININSTLIB=lib
else
CMAKE_CONFIG_FLAGS += -DBUILD_SHARED_LIBS=OFF
WININSTLIB=staticlib
endif
endif
ifneq ($(findstring debug,$(EPICS_HOST_ARCH)),)
CMAKE_CONFIG_FLAGS += -DCMAKE_BUILD_TYPE:STRING=Debug
CMAKE_CONFIG=Debug
else
CMAKE_CONFIG_FLAGS += -DCMAKE_BUILD_TYPE:STRING=Release
CMAKE_CONFIG=Release
endif
CMAKE_CONFIG_FLAGS += -DBUILD_PERF_TESTS:BOOL=OFF -DBUILD_TESTS:BOOL=OFF -DBUILD_DOCS:BOOL=OFF  -DWITH_CUDA:BOOL=OFF -DBUILD_EXAMPLES:BOOL=OFF # -DINSTALL_CREATE_DISTRIB=ON

include $(TOP)/configure/RULES
#----------------------------------------
#  ADD RULES AFTER THIS LINE

ifdef T_A
install:
	$(CMAKE) $(OPENCV_SRC) -G "$(CMAKE_GENERATOR)" -A "$(PLATFORM)" -DCMAKE_INSTALL_PREFIX:PATH="$(OPENCV_INSTALL)" $(CMAKE_CONFIG_FLAGS)
	$(CMAKE) --build . --target install --config $(CMAKE_CONFIG) $(CMAKE_BUILD_FLAGS)
	-$(MKDIR) $(TOP)/bin
	-$(MKDIR) $(TOP)/bin/$(EPICS_HOST_ARCH)
	-$(MKDIR) $(TOP)/lib
	-$(MKDIR) $(TOP)/lib/$(EPICS_HOST_ARCH)
	$(CMAKE) -E remove_directory $(TOP)/include
	$(CMAKE) -E copy_directory $(OPENCV_INSTALL)/include $(TOP)/include
ifeq ($(findstring linux,$(EPICS_HOST_ARCH)),)
	$(CP) -f $(OPENCV_INSTALL)/*/*/bin/*.exe $(TOP)/bin/$(EPICS_HOST_ARCH)/
	$(CP) -f $(OPENCV_INSTALL)/*/*/bin/*.dll $(TOP)/bin/$(EPICS_HOST_ARCH)/
	$(CP) -f $(OPENCV_INSTALL)/*/*/$(WININSTLIB)/*.lib $(TOP)/lib/$(EPICS_HOST_ARCH)/
	$(CP) -f $(OPENCV_INSTALL)/*/*/$(WININSTLIB)/opencv_core*.lib $(TOP)/lib/$(EPICS_HOST_ARCH)/opencv_core.lib
	$(CP) -f $(OPENCV_INSTALL)/*/*/$(WININSTLIB)/opencv_imgproc*.lib $(TOP)/lib/$(EPICS_HOST_ARCH)/opencv_imgproc.lib
else
	$(CP) -f $(OPENCV_INSTALL)/lib*/libopencv* $(TOP)/lib/$(EPICS_HOST_ARCH)/
endif
endif

#clean::
#	$(MAKE) -C mysql-connector-c -f Makefile.epics clean T_A=$(T_A) CMAKE_CONFIG=$(CMAKE_CONFIG) PLATFORM=$(PLATFORM) MYSQL_DIR="$(MYSQL_DIR)"
#	$(MAKE) -C mysql-connector-c++ -f Makefile.epics clean T_A=$(T_A) CMAKE_CONFIG=$(CMAKE_CONFIG) PLATFORM=$(PLATFORM) MYSQL_DIR="$(MYSQL_DIR)"
#
#uninstall:
#	$(RMDIR) $(MYSQL_DIR)
#
