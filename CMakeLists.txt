###########################################################################
# This file is part of LImA, a Library for Image Acquisition
#
#  Copyright (C) : 2009-2017
#  European Synchrotron Radiation Facility
#  CS40220 38043 Grenoble Cedex 9 
#  FRANCE
# 
#  Contact: lima@esrf.fr
# 
#  This is free software; you can redistribute it and/or modify
#  it under the terms of the GNU General Public License as published by
#  the Free Software Foundation; either version 3 of the License, or
#  (at your option) any later version.
# 
#  This software is distributed in the hope that it will be useful,
#  but WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#  GNU General Public License for more details.
# 
#  You should have received a copy of the GNU General Public License
#  along with this program; if not, see <http://www.gnu.org/licenses/>.
############################################################################

set(CMAKE_SHARED_LINKER_FLAGS "-Wl,--add-needed")

#SOURCES
set(xspress3_srcs src/Xspress3Camera.cpp  src/Xspress3Interface.cpp
	 src/Xspress3DetInfoCtrlObj.cpp src/Xspress3SyncCtrlObj.cpp)

file(STRINGS "VERSION" xspress3_vers)
add_library(limaxspress3 SHARED ${xspress3_srcs})

#INCLUDES
target_include_directories(limaxspress3 PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include")
target_include_directories(limaxspress3 PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/sdk/include")
target_include_directories(limaxspress3 PUBLIC "${CMAKE_SOURCE_DIR}/third-party/Processlib/sip")
target_include_directories(limaxspress3 PUBLIC "${CMAKE_SOURCE_DIR}/camera/common/espia/include")

#LIBRARIES
find_library(XSPRESS3_LIB xspress3 ${CMAKE_CURRENT_SOURCE_DIR}/sdk/libs/linux.x86_64)
find_library(XSPRESS3_LIB_IMGMOD img_mod ${CMAKE_CURRENT_SOURCE_DIR}/sdk/libs/linux.x86_64)
if("${XSPRESS3_LIB}" STREQUAL "XSPRESS3_LIB-NOTFOUND")
  message(FATAL_ERROR "xspress3 : libxspress3 not found, you can search it in cmake-gui or try to install it if it isn't here.")
endif()
if("${XSPRESS3_LIB_IMGMOD}" STREQUAL "XSPRESS3_LIB_IMGMOD-NOTFOUND")
  message(FATAL_ERROR "xspress3 : libimg_mod not found, you can search it in cmake-gui or try to install it if it isn't here.")
endif()

target_link_libraries(limaxspress3 limacore)
target_link_libraries(limaxspress3 ${XSPRESS3_LIB})
target_link_libraries(limaxspress3 ${XSPRESS3_LIB_IMGMOD})

set_target_properties(limaxspress3 PROPERTIES VERSION ${xspress3_vers} SOVERSION "1")

install(TARGETS limaxspress3 LIBRARY DESTINATION lib)

if(COMPILE_SIP)
    set(NAME "xspress3")
    include(CameraRunSIP)
    install(FILES python/__init__.py DESTINATION "${PYTHON_SITE_PACKAGES_DIR}/Lima/Xspress3")
endif()

if(COMPILE_TEST)
	add_subdirectory(test/)
endif()
