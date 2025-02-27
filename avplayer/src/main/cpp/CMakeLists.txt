# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.10.2)

# Declares and names the project.

project("avplayer")

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds them for you.
# Gradle automatically packages shared libraries with your APK.

include_directories(${CMAKE_SOURCE_DIR}/avplayer/include)
include_directories(${CMAKE_SOURCE_DIR}/avplayer/decode/include)
include_directories(${CMAKE_SOURCE_DIR}/avplayer/decode/data)
include_directories(${CMAKE_SOURCE_DIR}/utils/include)
include_directories(${CMAKE_SOURCE_DIR}/avplayer/dispatcher/include)
include_directories(${CMAKE_SOURCE_DIR}/avplayer/render/include)
include_directories(${CMAKE_SOURCE_DIR}/avplayer/stream/include)

add_library( # Sets the name of the library.
        native-lib

        # Sets the library as a shared library.
        SHARED

        # Provides a relative path to your source file(s).
        native-lib.cpp
        avplayer/AVPlayer.cpp
        avplayer/decode/BaseDecoder.cpp
        avplayer/decode/AudioDecoder.cpp
        avplayer/decode/VideoDecoder.cpp
        avplayer/dispatcher/PacketDispatcher.cpp
        avplayer/render/AudioRender.cpp
        avplayer/render/VideoRender.cpp
        avplayer/render/BaseRender.cpp
        avplayer/render/RenderSynchronizer.cpp
        avplayer/stream/BaseStreamAware.cpp)

# Searches for a specified prebuilt library and stores the path as a
# variable. Because CMake includes system libraries in the search path by
# default, you only need to specify the name of the public NDK library
# you want to add. CMake verifies that the library exists before
# completing its build.

include_directories(${CMAKE_SOURCE_DIR}/ffmpeg/include)
set(lib_src_DIR ${CMAKE_SOURCE_DIR}/ffmpeg/libs/${CMAKE_ANDROID_ARCH_ABI})

add_library(avcodec SHARED IMPORTED)
set_target_properties(avcodec PROPERTIES IMPORTED_LOCATION
        ${lib_src_DIR}/libavcodec.so)

add_library(avdevice SHARED IMPORTED)
set_target_properties(avdevice PROPERTIES IMPORTED_LOCATION
        ${lib_src_DIR}/libavdevice.so)

add_library(avfilter SHARED IMPORTED)
set_target_properties(avfilter PROPERTIES IMPORTED_LOCATION
        ${lib_src_DIR}/libavfilter.so)

add_library(avformat SHARED IMPORTED)
set_target_properties(avformat PROPERTIES IMPORTED_LOCATION
        ${lib_src_DIR}/libavformat.so)

add_library(avutil SHARED IMPORTED)
set_target_properties(avutil PROPERTIES IMPORTED_LOCATION
        ${lib_src_DIR}/libavutil.so)

add_library(swresample SHARED IMPORTED)
set_target_properties(swresample PROPERTIES IMPORTED_LOCATION
        ${lib_src_DIR}/libswresample.so)

add_library(swscale SHARED IMPORTED)
set_target_properties(swscale PROPERTIES IMPORTED_LOCATION
        ${lib_src_DIR}/libswscale.so)

find_library( # Sets the name of the path variable.
        log-lib

        # Specifies the name of the NDK library that
        # you want CMake to locate.
        log )

# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in this
# build script, prebuilt third-party libraries, or system libraries.

target_link_libraries( # Specifies the target library.
        native-lib
        avcodec
        avdevice
        avfilter
        avformat
        avutil
        swresample
        swscale
        android
        OpenSLES
        # Links the target library to the log library
        # included in the NDK.
        ${log-lib} )