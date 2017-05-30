# CMakeLists.txt for Xspress3 camera

SET(CMAKE_SHARED_LINKER_FLAGS "-Wl,--add-needed")

SET(xspress3_srcs src/Xspress3Camera.cpp  src/Xspress3Interface.cpp
	 src/Xspress3DetInfoCtrlObj.cpp src/Xspress3SyncCtrlObj.cpp)

FILE(STRINGS "VERSION" xspress3_vers)
ADD_LIBRARY(limaxspress3 SHARED ${xspress3_srcs})

TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include")
TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/sdk/include")
TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_SOURCE_DIR}/third-party/Processlib/sip")
TARGET_INCLUDE_DIRECTORIES(limaxspress3 PUBLIC "${CMAKE_SOURCE_DIR}/camera/common/espia/include")

FIND_LIBRARY(XSPRESS3_LIB xspress3 ${CMAKE_CURRENT_SOURCE_DIR}/sdk/libs/linux.x86_64)
FIND_LIBRARY(XSPRESS3_LIB_IMGMOD img_mod ${CMAKE_CURRENT_SOURCE_DIR}/sdk/libs/linux.x86_64)

TARGET_LINK_LIBRARIES(limaxspress3 limacore)
TARGET_LINK_LIBRARIES(limaxspress3 ${XSPRESS3_LIB})
TARGET_LINK_LIBRARIES(limaxspress3 ${XSPRESS3_LIB_IMGMOD})

SET_TARGET_PROPERTIES(limaxspress3 PROPERTIES VERSION ${xspress3_vers} SOVERSION "1")

INSTALL(TARGETS limaxspress3 LIBRARY DESTINATION lib)

IF(COMPILE_SIP)
    SET(NAME "xspress3")
    SET(INCLUDES)
    FILE(GLOB sipfiles RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}/sip" "${CMAKE_CURRENT_SOURCE_DIR}/sip/*.sip")
    FOREACH(sipfile ${sipfiles})
        SET(INCLUDES 
        "${INCLUDES} 
        %Include ${sipfile}")
    ENDFOREACH()

    CONFIGURE_FILE(${CMAKE_SOURCE_DIR}/sip/limamodules.sip.in sip/limaxspress3.sip)
    SET(SIP_CONCAT_PARTS 1)
    SET(SIP_INCLUDES ${SIP_INCLUDES}
        "${CMAKE_SOURCE_DIR}/third-party/Processlib/sip"
        "${CMAKE_BINARY_DIR}/sip/core"
        "${CMAKE_SOURCE_DIR}/third-party/Processlib/tasks/sip"
        "${CMAKE_SOURCE_DIR}/common/sip"
        "${CMAKE_SOURCE_DIR}/hardware/sip"
        "${CMAKE_SOURCE_DIR}/control/sip"
        "${CMAKE_SOURCE_DIR}/control/software_operation/sip"
        "${CMAKE_CURRENT_SOURCE_DIR}/sip")
    set(SIP_DISABLE_FEATURES WITH_CONFIG)
    set(SIP_CHECK_EXC ON)
    ADD_SIP_PYTHON_MODULE(limaxspress3 ${CMAKE_CURRENT_BINARY_DIR}/sip/limaxspress3.sip)
    TARGET_INCLUDE_DIRECTORIES(python_module_limaxspress3 PRIVATE
                               ${PYTHON_INCLUDE_DIRS}
                               "${CMAKE_SOURCE_DIR}/sip"
                               "${CMAKE_SOURCE_DIR}/sip/core")
    TARGET_LINK_LIBRARIES(python_module_limaxspress3 limaxspress3)

    INSTALL(FILES python/__init__.py DESTINATION "${PYTHON_SITE_PACKAGES_DIR}/Lima/Xspress3")
ENDIF()
