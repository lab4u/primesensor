cmake_minimum_required(VERSION 2.8)

project(SensorKin4Win)

find_package(openni REQUIRED)
find_package(jpeg   REQUIRED NO_MODULE)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/Include
    ${CMAKE_CURRENT_SOURCE_DIR}/Source
    ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnCommon
)

################################################################################

# XnKin4WinCore

file(GLOB XnKin4WinCore_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnCore/*.cpp)
add_library(XnKin4WinCore SHARED ${XnKin4WinCore_SOURCES})
target_link_libraries(XnKin4WinCore
    OpenNI
)
set_property(TARGET XnKin4WinCore PROPERTY COMPILE_DEFINITIONS XN_CORE_EXPORTS)

# XnKin4WinFormats

file(GLOB XnKin4WinFormats_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnFormats/*.cpp)
add_library(XnKin4WinFormats SHARED ${XnKin4WinFormats_SOURCES})
target_link_libraries(XnKin4WinFormats
    jpeg
    XnKin4WinCore
)
set_property(TARGET XnKin4WinFormats PROPERTY COMPILE_DEFINITIONS XN_FORMATS_EXPORTS)

# XnKin4WinDDK

file(GLOB XnKin4WinDDK_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDDK/*.cpp)
add_library(XnKin4WinDDK SHARED ${XnKin4WinDDK_SOURCES})
target_link_libraries(XnKin4WinDDK
    XnKin4WinCore
    nimCodecs
    jpeg
    XnKin4WinFormats
)
set_property(TARGET XnKin4WinDDK PROPERTY COMPILE_DEFINITIONS XN_DDK_EXPORTS)

# XnKin4WinDeviceFile

file(GLOB XnKin4WinDeviceFile_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceFile/*.cpp)
add_library(XnKin4WinDeviceFile SHARED ${XnKin4WinDeviceFile_SOURCES})
target_link_libraries(XnKin4WinDeviceFile
    XnKin4WinFormats
    XnKin4WinDDK
)
set_property(TARGET XnKin4WinDeviceFile PROPERTY COMPILE_DEFINITIONS XN_DEVICE_EXPORTS)

# XnKin4WinDeviceSensorV2

file(GLOB XnKin4WinDevicesSensorV2_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceSensorV2/*.cpp)
add_library(XnKin4WinDevicesSensorV2 SHARED ${XnKin4WinDevicesSensorV2_SOURCES})
target_link_libraries(XnKin4WinDevicesSensorV2
    XnKin4WinFormats
    XnKin4WinDDK
)
set_property(TARGET XnKin4WinDevicesSensorV2 PROPERTY COMPILE_DEFINITIONS XN_DEVICE_EXPORTS)

# XnKin4WinSensorServer

file(GLOB XnKin4WinSensorServer_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/Utils/XnSensorServer/*.cpp)
add_executable(XnKin4WinSensorServer ${XnKin4WinSensorServer_SOURCES})
target_link_libraries(XnKin4WinSensorServer
    XnKin4WinDDK
    XnKin4WinFormats
    XnKin4WinDevicesSensorV2
    XnKin4WinCore
    OpenNI
    jpeg
)

################################################################################

install(TARGETS
    XnKin4WinCore
    XnKin4WinFormats
    XnKin4WinDDK
    XnKin4WinDeviceFile
    XnKin4WinDevicesSensorV2
    XnKin4WinSensorServer

    EXPORT sensorkin4win-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)
install(FILES  sensorkin4win-config.cmake DESTINATION share/sensorkin4win)
install(EXPORT sensorkin4win-exports      DESTINATION share/sensorkin4win)
