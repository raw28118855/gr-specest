# Copyright 2011,2012,2016,2018 Free Software Foundation, Inc.
#
# This file was generated by gr_modtool, a tool from the GNU Radio framework
# This file is a part of gr-specest
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

########################################################################
# Fortran lib
########################################################################
add_library(gnuradio-specest-fortran SHARED
    zcorrest_algo.f90
    zesprit_common_algo.f90
    zesprit_algo.f90
    zesprit_spectrum_algo.f90
    zmusic_common_algo.f90
    zmusic_spectrum_algo.f90
    zmusic_algo.f90
)
target_link_libraries(gnuradio-specest-fortran
    ${LAPACK_LIBRARIES}
    ${BLAS_LIBRARIES})

########################################################################
# Configure Armadillo dependency
########################################################################
if(ARMADILLO_FOUND)
    set(CXX_ARMADILLO_SRCS
        correst_algo.cc
        music_armadillo_algo.cc
        music_vcf_impl.cc
        music_impl.cc
        music_spectrum_vcf_impl.cc
        )
else()
    set(CXX_ARMADILLO_SRCS)
    set(ARMADILLO_LIBRARIES)
endif()

########################################################################
# Main library
########################################################################
list(APPEND specest_sources
    reciprocal_ff_impl.cc
    pad_vector_impl.cc
    stream_to_vector_overlap_impl.cc
    moving_average_vff_impl.cc
    adaptiveweighting_vff_impl.cc
    arfcov_algo.cc
    arfcov_vcc_impl.cc
    fcov_impl.cc
    arfmcov_algo.cc
    arfmcov_vcc_impl.cc
    fmcov_impl.cc
    arburg_algo.cc
    arburg_vcc_impl.cc
    burg_impl.cc
    welch_impl.cc
    esprit_fortran_algo.cc
    esprit_vcf_impl.cc
    esprit_spectrum_vcf_impl.cc
    esprit_impl.cc
    music_fortran_algo.cc
    welchsp_impl.cc
    cyclo_fam_calcspectrum_algo.cc
    cyclo_fam_calcspectrum_vcf_impl.cc
    cyclo_fam_impl.cc
    ${CXX_ARMADILLO_SRCS}
)

add_library(gnuradio-specest SHARED ${specest_sources})
target_link_libraries(gnuradio-specest gnuradio-specest-fortran
    gnuradio::gnuradio-runtime
    gnuradio::gnuradio-blocks
    gnuradio::gnuradio-filter
    gnuradio::gnuradio-fft
    ${ARMADILLO_LIBRARIES}
    ${FFTW3F_LIBRARIES}
)
target_include_directories(gnuradio-specest
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    PUBLIC $<INSTALL_INTERFACE:include>
)
set_target_properties(gnuradio-specest PROPERTIES DEFINE_SYMBOL "gnuradio_specest_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-specest PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

########################################################################
# Install built library files
########################################################################
include(GrMiscUtils)
GR_LIBRARY_FOO(gnuradio-specest)
GR_LIBRARY_FOO(gnuradio-specest-fortran)

########################################################################
# Print summary
########################################################################
message(STATUS "Using install prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "Building for version: ${VERSION} / ${LIBVER}")

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

# If your unit tests require special include paths, add them here
#include_directories()
# List all files that contain Boost.UTF unit tests here
list(APPEND test_specest_sources
)
# Anything we need to link to for the unit tests go here
list(APPEND GR_TEST_TARGET_DEPS
    gnuradio::gnuradio-runtime
    gnuradio::gnuradio-blocks
    gnuradio::gnuradio-filter
    gnuradio::gnuradio-fft
    ${Boost_LIBRARIES}
    ${CPPUNIT_LIBRARIES}
    ${FFTW3F_LIBRARIES}
    gnuradio-specest
)

if(NOT test_specest_sources)
    MESSAGE(STATUS "No C++ unit tests... skipping")
    return()
endif(NOT test_specest_sources)

foreach(qa_file ${test_specest_sources})
    GR_ADD_CPP_TEST("specest_${qa_file}"
        ${CMAKE_CURRENT_SOURCE_DIR}/${qa_file}
    )
endforeach(qa_file)

