############################################################################
# This file is part of LImA, a Library for Image Acquisition
#
# Copyright (C) : 2009-2017
# European Synchrotron Radiation Facility
# BP 220, Grenoble 38043
# FRANCE
#
# This is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 3 of the License, or
# (at your option) any later version.
#
# This software is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, see <http://www.gnu.org/licenses/>.
############################################################################
# CMakeLists.txt for Xspress3 camera

SET(CMAKE_SHARED_LINKER_FLAGS "-Wl,--add-needed")

#SOURCES
SET(xspress3_srcs src/Xspress3Camera.cpp  src/Xspress3Interface.cpp
	 src/Xspress3DetInfoCtrlObj.cpp src/Xspress3SyncCtrlObj.cpp)

FILE(STRINGS "VERSION" xspress3_vers)
ADD_LIBRARY(limaxspress3 SHARED ${xspress3_srcs})

#INCLUDES
TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include")
TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/sdk/include")
TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_SOURCE_DIR}/third-party/Processlib/sip")
TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_SOURCE_DIR}/camera/common/espia/include")

#LIBRARIES
FIND_LIBRARY(XSPRESS3_LIB xspress3 ${CMAKE_CURRENT_SOURCE_DIR}/sdk/libs/linux.x86_64)
FIND_LIBRARY(XSPRESS3_LIB_IMGMOD img_mod ${CMAKE_CURRENT_SOURCE_DIR}/sdk/libs/linux.x86_64)
IF("${XSPRESS3_LIB}" STREQUAL "XSPRESS3_LIB-NOTFOUND")
  MESSAGE(FATAL_ERROR "xspress3 : libxspress3 not found, you can search it in cmake-gui or try to install it if it isn't here.")
ENDIF()
IF("${XSPRESS3_LIB_IMGMOD}" STREQUAL "XSPRESS3_LIB_IMGMOD-NOTFOUND")
  MESSAGE(FATAL_ERROR "xspress3 : libimg_mod not found, you can search it in cmake-gui or try to install it if it isn't here.")
ENDIF()

TARGET_LINK_LIBRARIES(limaxspress3 limacore)
TARGET_LINK_LIBRARIES(limaxspress3 ${XSPRESS3_LIB})
TARGET_LINK_LIBRARIES(limaxspress3 ${XSPRESS3_LIB_IMGMOD})

SET_TARGET_PROPERTIES(limaxspress3 PROPERTIES VERSION ${xspress3_vers} SOVERSION "1")

INSTALL(TARGETS limaxspress3 LIBRARY DESTINATION lib)

IF(COMPILE_SIP)
    SET(NAME "xspress3")
    include(CameraRunSIP)
    INSTALL(FILES python/__init__.py DESTINATION "${PYTHON_SITE_PACKAGES_DIR}/Lima/Xspress3")
ENDIF()
