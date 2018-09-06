# Copyright (c) 2015-present, Facebook, Inc.
# All rights reserved.
#
# This source code is licensed under the BSD+Patents license found in the
# LICENSE file in the root directory of this source tree.

-include ../makefile.inc

HEADERS = $(wildcard ../*.h)

all: cpu

#############################
# CPU

cpu: faiss.jar libswigfaiss.$(SHAREDEXT)

# Also silently generates swigfaiss.py.
swigfaiss_wrap.cpp: swigfaiss.swig $(HEADERS)
	$(SWIG) -java -package com.facebook.research.faiss -c++ -Doverride= -I../ -o $@ $<
	# We have to have a custom patch to avoid problems with the finalize method
	patch -p0 < RangeSearchPartialResult.patch

swigfaiss_wrap.o: swigfaiss_wrap.cpp
	$(CXX) $(CXXFLAGS) $(CPUFLAGS) $(JAVACFLAGS) -I../ -c $< -o $@

libswigfaiss.$(SHAREDEXT): swigfaiss_wrap.o ../libfaiss.a
	$(CXX) $(SHAREDFLAGS) $(LDFLAGS) -o $@ $^  $(LIBS)

swigfaiss.java: swigfaiss_wrap.cpp

com/facebook/research/faiss/swigfaiss.class: swigfaiss.java
	javac -d . *java

faiss.jar: com/facebook/research/faiss/swigfaiss.class
	jar -cvf faiss.jar com/facebook/research/faiss/*.class


#############################
# GPU
#TODO
clean:
	rm -f swigfaiss_wrap.cpp

.PHONY: all clean cpu
