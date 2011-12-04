cmake_minimum_required(VERSION 2.8)

project(PrimeSensor)

find_package(openni REQUIRED)
find_package(jpeg   REQUIRED NO_MODULE)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/Include
    ${CMAKE_CURRENT_SOURCE_DIR}/Source
)

################################################################################

# XnCore

file(GLOB XnCore_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnCore/*.cpp)
add_library(XnCore SHARED ${XnCore_SOURCES})
target_link_libraries(XnCore
    OpenNI
)

# XnFormats

file(GLOB XnFormats_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnFormats/*.cpp)
add_library(XnFormats SHARED ${XnFormats_SOURCES})
target_link_libraries(XnFormats
    jpeg
    XnCore
)

# XnDDK

file(GLOB XnDDK_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDDK/*.cpp)
add_library(XnDDK SHARED ${XnDDK_SOURCES})
target_link_libraries(XnDDK
    XnCore
    nimCodecs
    jpeg
    XnFormats
)

# XnDeviceFile

file(GLOB XnDeviceFile_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceFile/*.cpp)
add_library(XnDeviceFile SHARED ${XnDeviceFile_SOURCES})
target_link_libraries(XnDeviceFile
    XnFormats
    XnDDK
)

# XnDeviceSensorV2

file(GLOB XnDevicesSensorV2_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceSensorV2/*.cpp)
add_library(XnDevicesSensorV2 SHARED ${XnDevicesSensorV2_SOURCES})
target_link_libraries(XnDevicesSensorV2
    XnFormats
    XnDDK
)

# XnSensorServer

file(GLOB XnSensorServer_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/Utils/XnSensorServer/*.cpp)
add_executable(XnSensorServer ${XnSensorServer_SOURCES})
target_link_libraries(XnSensorServer
    XnDDK
    XnFormats
    XnDevicesSensorV2
    XnCore
    OpenNI
    jpeg
)

################################################################################

# FIXME: Is this required?
set_target_properties(XnSensorServer PROPERTIES INSTALL_RPATH ".")
install(TARGETS XnSensorServer EXPORT primesensor-exports
    LIBRARY DESTINATION .
    RUNTIME DESTINATION .
)

install(TARGETS
    XnCore
    XnFormats
    XnDDK
    XnDeviceFile
    XnDevicesSensorV2
#    XnSensorServer

    EXPORT primesensor-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

install(FILES  primesensor-config.cmake DESTINATION share/primesensor)
install(EXPORT primesensor-exports      DESTINATION share/primesensor)
