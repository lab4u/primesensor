cmake_minimum_required(VERSION 2.8)

project(SensorKinect)

find_package(openni REQUIRED)
find_package(jpeg   REQUIRED NO_MODULE)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/Include
    ${CMAKE_CURRENT_SOURCE_DIR}/Source
)

################################################################################

# XnKinectCore

file(GLOB XnKinectCore_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnCore/*.cpp)
add_library(XnKinectCore SHARED ${XnKinectCore_SOURCES})
target_link_libraries(XnKinectCore
    OpenNI
)

# XnKinectFormats

file(GLOB XnKinectFormats_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnFormats/*.cpp)
add_library(XnKinectFormats SHARED ${XnKinectFormats_SOURCES})
target_link_libraries(XnKinectFormats
    jpeg
    XnKinectCore
)

# XnKinectDDK

file(GLOB XnKinectDDK_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDDK/*.cpp)
add_library(XnKinectDDK SHARED ${XnKinectDDK_SOURCES})
target_link_libraries(XnKinectDDK
    XnKinectCore
    nimCodecs
    jpeg
    XnKinectFormats
)

# XnKinectDeviceFile

file(GLOB XnKinectDeviceFile_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceFile/*.cpp)
add_library(XnKinectDeviceFile SHARED ${XnKinectDeviceFile_SOURCES})
target_link_libraries(XnKinectDeviceFile
    XnKinectFormats
    XnKinectDDK
)

# XnKinectDeviceSensorV2

file(GLOB XnKinectDevicesSensorV2_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceSensorV2/*.cpp)
add_library(XnKinectDevicesSensorV2 SHARED ${XnKinectDevicesSensorV2_SOURCES})
target_link_libraries(XnKinectDevicesSensorV2
    XnKinectFormats
    XnKinectDDK
)

# XnKinectSensorServer

file(GLOB XnKinectSensorServer_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/Utils/XnSensorServer/*.cpp)
add_executable(XnKinectSensorServer ${XnKinectSensorServer_SOURCES})
target_link_libraries(XnKinectSensorServer
    XnKinectDDK
    XnKinectFormats
    XnKinectDevicesSensorV2
    XnKinectCore
    OpenNI
    jpeg
)

################################################################################

install(TARGETS
    XnKinectCore
    XnKinectFormats
    XnKinectDDK
    XnKinectDeviceFile
    XnKinectDevicesSensorV2
    XnKinectSensorServer

    EXPORT sensorkinect-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)
install(FILES  sensorkinect-config.cmake DESTINATION share/sensorkinect)
install(EXPORT sensorkinect-exports      DESTINATION share/sensorkinect)
