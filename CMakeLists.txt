# Uncompilable CMake File to enable project editing with CLion IDE

cmake_minimum_required(VERSION 2.8.4)
project(arduino_mqtt)

include_directories(/Applications/Arduino.app/Contents/Java/hardware/arduino/avr/cores/arduino/)
include_directories(/Applications/Arduino.app/Contents/Java/libraries/Bridge/src)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

set(SOURCE_FILES
    src/lib/FP.h
    src/lib/PahoMQTTClient.h
    src/lib/PahoMQTTConnect.h
    src/lib/PahoMQTTConnectClient.c
    src/lib/PahoMQTTDeserializePublish.c
    src/lib/PahoMQTTPacket.c
    src/lib/PahoMQTTPacket.h
    src/lib/PahoMQTTPublish.h
    src/lib/PahoMQTTSerializePublish.c
    src/lib/PahoMQTTSubscribe.h
    src/lib/PahoMQTTSubscribeClient.c
    src/lib/PahoMQTTUnsubscribe.h
    src/lib/PahoMQTTUnsubscribeClient.c
    src/PahoMQTTClient.cpp
    src/PahoMQTTClient.h
    src/PahoMQTTTest.h
    src/Network.cpp
    src/Network.h
    src/Timer.cpp
    src/Timer.h
    src/YunPahoMQTTClient.cpp
    src/YunPahoMQTTClient.h)

set(ARDUINO_AVR_YUN 1)

add_executable(arduino_mqtt ${SOURCE_FILES})
