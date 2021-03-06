# Copyright (c) 2014-2015, Intel Corporation All rights reserved. 
# 
# Redistribution and use in source and binary forms, with or without 
# modification, are permitted provided that the following conditions are 
# met: 
# 
# 1. Redistributions of source code must retain the above copyright 
# notice, this list of conditions and the following disclaimer. 
#
# 2. Redistributions in binary form must reproduce the above copyright 
# notice, this list of conditions and the following disclaimer in the 
# documentation and/or other materials provided with the distribution. 
#
# 3. Neither the name of the copyright holder nor the names of its 
# contributors may be used to endorse or promote products derived from 
# this software without specific prior written permission. 
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS 
# IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED 
# TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A 
# PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT 
# HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, 
# SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED 
# TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR 
# PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF 
# LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING 
# NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS 
# SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE. 

.PHONY: all clean realclean

ifeq ($(debug),0)
DEBUG=
else
# DEBUG=-DDEBUG
endif

PYMIC_OBJECTS=pymicimpl_interface.o pymicimpl_misc.o
XSTREAM_OBJECTS=libxstream/src/libxstream.o libxstream/src/libxstream_capture.o \
                libxstream/src/libxstream_event.o libxstream/src/libxstream_stream.o \
				libxstream/src/libxstream_alloc.o libxstream/src/libxstream_argument.o \
				libxstream/src/libxstream_context.o libxstream/src/libxstream_offload.o

PYTHON_INCLUDES+=-I/usr/include/python2.7 -I/usr/lib64/python2.7/site-packages/numpy/core/include

CC_MIC=icc
CFLAGS_MIC=-DPYMIC_USE_XSTREAM=1 -I../include -fPIC -shared -mmic -g -O2 -o
CXX=icpc
CXXFLAGS=$(DEBUG) -DNDEBUG -DPYMIC_USE_XSTREAM=1 -DLIBXSTREAM_EXPORTED -offload=mandatory -Wall -pthread -g -O2 -ansi-alias -fPIC -I. -I../include -I./libxstream/include $(PYTHON_INCLUDES)
LD=icpc
LDFLAGS=-shared -pthread -offload-option,mic,link,"--no-undefined"

all: ../pymic/_pymicimpl.so _pymicimpl.so ../pymic/liboffload_array.so liboffload_array.so

_pymicimpl.so: $(PYMIC_OBJECTS) $(XSTREAM_OBJECTS)
	$(LD) $(LDFLAGS) -o $@ $(PYMIC_OBJECTS) $(XSTREAM_OBJECTS)
	
../pymic/_pymicimpl.so: _pymicimpl.so
	cp _pymicimpl.so ../pymic/_pymicimpl.so
	chmod a+rX ../pymic/_pymicimpl.so

pymicimpl_interface.cc: debug.h pymicimpl_config.h pymicimpl_common.h pymicimpl_misc.h pymicimpl_misc.h
pymicimpl_misc.cc:      debug.h pymicimpl_config.h pymicimpl_common.h pymicimpl_misc.h 


liboffload_array.so: offload_array.c
	$(CC_MIC) $(CFLAGS_MIC) $@ $< -lm
	chmod a+rX liboffload_array.so

offload_array.c: ../include/pymic_kernel.h
	
../pymic/liboffload_array.so: liboffload_array.so
	cp liboffload_array.so ../pymic/liboffload_array.so
	chmod a+rX ../pymic/liboffload_array.so
	
clean:
	rm -f $(PYMIC_OBJECTS)
	rm -f $(XSTREAM_OBJECTS)

realclean: clean
	rm -f *.bak *~
	rm -f *.so
