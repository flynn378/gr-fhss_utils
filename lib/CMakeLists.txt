# Copyright 2011,2012,2016 Free Software Foundation, Inc.
#
# This file is part of GNU Radio
#
# GNU Radio is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3, or (at your option)
# any later version.
#
# GNU Radio is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with GNU Radio; see the file COPYING.  If not, write to
# the Free Software Foundation, Inc., 51 Franklin Street,
# Boston, MA 02110-1301, USA.

########################################################################
# Setup library
########################################################################
include(GrPlatform) #define LIB_SUFFIX

include_directories(${Boost_INCLUDE_DIR})
link_directories(${Boost_LIBRARY_DIRS})

list(APPEND fhss_utils_sources
    fft_burst_tagger_impl.cc
    tagged_burst_to_pdu_impl.cc
    burst_downmix_impl.cc
    pdu_quadrature_demod_cf_impl.cc
    burst_measure_impl.cc
    fine_burst_measure_impl.cc
    fsk_cfo_estimate_impl.cc
    fft_cfo_estimate_impl.cc
    coerce_frequency_estimate_impl.cc
)

set(fhss_utils_sources "${fhss_utils_sources}" PARENT_SCOPE)
if(NOT fhss_utils_sources)
	MESSAGE(STATUS "No C++ sources... skipping lib/")
	return()
endif(NOT fhss_utils_sources)

add_library(gnuradio-fhss_utils SHARED ${fhss_utils_sources})
target_link_libraries(gnuradio-fhss_utils 
                      gnuradio::gnuradio-runtime  
                      gnuradio::gnuradio-filter 
                      gnuradio::gnuradio-fft)
target_include_directories(gnuradio-fhss_utils
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/>
    PUBLIC $<INSTALL_INTERFACE:include>
  )
set_target_properties(gnuradio-fhss_utils PROPERTIES DEFINE_SYMBOL "gnuradio_fhss_utils_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-fhss_utils PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

##set_target_properties(gnuradio-iridium PROPERTIES CXX_STANDARD_REQUIRED 11)
#set(CMAKE_CXX_FLAGS "-fopenmp -std=c++11  -O2")

########################################################################
# Install built library files
########################################################################
include(GrMiscUtils)
GR_LIBRARY_FOO(gnuradio-fhss_utils)

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

list(APPEND test_fhss_utils_sources
)
# Anything we need to link to for the unit tests go here
list(APPEND GR_TEST_TARGET_DEPS gnuradio-fhss_utils)

if(NOT test_fhss_utils_sources)
    MESSAGE(STATUS "No C++ unit tests... skipping")
    return()
endif(NOT test_fhss_utils_sources)

foreach(qa_file ${test_fhss_utils_sources})
    GR_ADD_CPP_TEST("fhss_utils_${qa_file}"
        ${CMAKE_CURRENT_SOURCE_DIR}/${qa_file}
    )
endforeach(qa_file)

#GR_ADD_TEST(test_fhss_utils test-fhss_utils)

########################################################################
# Print summary
########################################################################
message(STATUS "Using install prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "Building for version: ${VERSION} / ${LIBVER}")

