#
# Copyright 2013 by Idiap Research Institute, http://www.idiap.ch
#
# See the file COPYING for the licence associated with this software.
#
# Author(s):
#   Phil Garner, December 2013
#

cmake_minimum_required(VERSION 3.0)

project(libssp)
set(VERSION 0.1)

set(CMAKE_CXX_FLAGS "-Wall -Werror -std=c++11")
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

if (NOT EXISTS "${CMAKE_MODULE_PATH}/FindLibUBE.cmake")
  message(STATUS "Downloading FindLibUBE.cmake")
  file(DOWNLOAD
    "https://github.com/pgarner/libube/raw/master/cmake/FindLibUBE.cmake"
    "${CMAKE_MODULE_PATH}/FindLibUBE.cmake"
    )
endif()

if (NOT LIBUBE_FOUND)
  find_package(LibUBE REQUIRED)
endif (NOT LIBUBE_FOUND)
include_directories(${LIBUBE_INCLUDE_DIRS})
set(TARGET_LIBS ${LIBUBE_LIBRARIES})

add_subdirectory(ssp)

add_executable(waveplot waveplot.cpp)
target_link_libraries(waveplot ssp-shared)

add_executable(specplot specplot.cpp)
target_link_libraries(specplot ssp-shared)

add_executable(varcoder varcoder.cpp)
target_link_libraries(varcoder ssp-shared)

set(INSTALL_TARGETS
  waveplot
  specplot
  varcoder
  )

install(
  TARGETS ${INSTALL_TARGETS}
  RUNTIME DESTINATION bin
  )

enable_testing()
add_subdirectory(test)

# pkgconfig install lines
set(PREFIX ${CMAKE_INSTALL_PREFIX})
set(EXEC_PREFIX "\${prefix}")
set(LIBDIR "\${exec_prefix}/lib")
set(INCLUDEDIR "\${prefix}/include")
configure_file(libssp.pc.in ${CMAKE_CURRENT_BINARY_DIR}/libssp.pc @ONLY)

install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/libssp.pc
  DESTINATION lib/pkgconfig
  )
