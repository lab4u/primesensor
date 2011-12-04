cmake_minimum_required(VERSION 2.8)

project(SensorKinect)

set(OPENNI_LIB_NAME OpenNI)
set(JPEG_LIB_NAME   jpeg)

find_package(openni REQUIRED)
find_package(jpeg   REQUIRED NO_MODULE)

if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
        set(SENSOR_PLATFORM_LINUX true)
elseif(WIN32)
        set(SENSOR_PLATFORM_WIN32 true)
elseif(APPLE)
        set(SENSOR_PLATFORM_MAC true)
endif()

set(           XNCORE_LIB_NAME "XnKinectCore")
set(            XNDDK_LIB_NAME "XnKinectDDK")
set(         XNFORMAT_LIB_NAME "XnKinectFormats")
set(     XNDEVICEFILE_LIB_NAME "XnKinectDeviceFile")
set(   XNDEVICESENSOR_LIB_NAME "XnKinectDevicesSensorV2")

set(XNSENSORSERVER_TARGET_NAME "XnKinectSensorServer")

set( OPENNI_INCLUDE_DIR "${OPENNI_BASE_DIR}/Include/")
set(     OPENNI_LIB_DIR "${OPENNI_BASE_DIR}/Platform/Mac/Bin/Release/")
set(LIBJPEG_INCLUDE_DIR "${OPENNI_BASE_DIR}/Source/External/LibJPEG/")

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/Include
    ${CMAKE_CURRENT_SOURCE_DIR}/Source
          ${OPENNI_INCLUDE_DIR}
            ${JPEG_INCLUDE_DIR}
)

################################################################################
# XnCore

file(GLOB ${XNCORE_LIB_NAME}_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnCore/*.cpp)

add_library(${XNCORE_LIB_NAME} SHARED ${${XNCORE_LIB_NAME}_SOURCES})

target_link_libraries(${XNCORE_LIB_NAME} ${OPENNI_LIB_NAME})
#target_link_libraries(${NIMMOCKNODES_LIB_NAME} ${OPENNI_LIB_NAME})

install(TARGETS ${XNCORE_LIB_NAME} EXPORT sensorkinect-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

################################################################################
# XnFormats

file(GLOB ${XNFORMAT_LIB_NAME}_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnFormats/*.cpp)

add_library(${XNFORMAT_LIB_NAME} SHARED ${${XNFORMAT_LIB_NAME}_SOURCES})

target_link_libraries(${XNFORMAT_LIB_NAME}
      ${JPEG_LIB_NAME}
    ${XNCORE_LIB_NAME}
)

install(TARGETS ${XNFORMAT_LIB_NAME} EXPORT sensorkinect-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

################################################################################
# XnDDK

file(GLOB ${XNDDK_LIB_NAME}_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDDK/*.cpp)

add_library(${XNDDK_LIB_NAME} SHARED ${${XNDDK_LIB_NAME}_SOURCES})

target_link_libraries(${XNDDK_LIB_NAME}
       ${XNCORE_LIB_NAME}
    ${NIMCODECS_LIB_NAME}
      ${LIBJPEG_LIB_NAME}
     ${XNFORMAT_LIB_NAME}
)

install(TARGETS ${XNDDK_LIB_NAME} EXPORT sensorkinect-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

################################################################################
# XnDeviceFile

file(GLOB ${XNDEVICEFILE_LIB_NAME}_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceFile/*.cpp)

add_library(${XNDEVICEFILE_LIB_NAME} SHARED ${${XNDEVICEFILE_LIB_NAME}_SOURCES})

target_link_libraries(${XNDEVICEFILE_LIB_NAME}
    ${XNFORMAT_LIB_NAME}
       ${XNDDK_LIB_NAME}
)

install(TARGETS ${XNDEVICEFILE_LIB_NAME} EXPORT sensorkinect-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

################################################################################
# XnDeviceSensorV2

file(GLOB ${XNDEVICESENSOR_LIB_NAME}_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/XnDeviceSensorV2/*.cpp)

add_library(${XNDEVICESENSOR_LIB_NAME} SHARED ${${XNDEVICESENSOR_LIB_NAME}_SOURCES})

target_link_libraries(${XNDEVICESENSOR_LIB_NAME}
    ${XNFORMAT_LIB_NAME}
       ${XNDDK_LIB_NAME}
)
install(TARGETS ${XNDEVICESENSOR_LIB_NAME} EXPORT sensorkinect-exports
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

################################################################################
# XnSensorServer

file(GLOB ${XNSENSORSERVER_TARGET_NAME}_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/Source/Utils/XnSensorServer/*.cpp)

add_executable(${XNSENSORSERVER_TARGET_NAME} ${${XNSENSORSERVER_TARGET_NAME}_SOURCES})

target_link_libraries(${XNSENSORSERVER_TARGET_NAME}
    ${XNSENSORSERVER_LIB_NAME}
             ${XNDDK_LIB_NAME}
          ${XNFORMAT_LIB_NAME}
    ${XNDEVICESENSOR_LIB_NAME}
            ${XNCORE_LIB_NAME}
            ${OPENNI_LIB_NAME}
           ${LIBJPEG_LIB_NAME}
)

set_target_properties(${XNSENSORSERVER_TARGET_NAME} PROPERTIES
    INSTALL_RPATH "."
)

install(TARGETS ${XNSENSORSERVER_TARGET_NAME}  EXPORT sensorkinect-exports
    LIBRARY DESTINATION .
    RUNTIME DESTINATION .
)

install(FILES  sensorkinect-config.cmake DESTINATION share/sensorkinect)
install(EXPORT sensorkinect-exports      DESTINATION share/sensorkinect)
