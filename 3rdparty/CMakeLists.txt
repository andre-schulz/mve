cmake_minimum_required(VERSION 3.7)
set(CMAKE_CONFIGURATION_TYPES Release)

project(3rdparty)
include(ExternalProject)

set(LIBJPEG_FILENAME "jpegsr9b.zip")
set(LIBPNG_FILENAME "lpng1629.7z")
set(ZLIB_FILENAME "zlib-1.2.11.tar.xz")
set(LIBTIFF_FILENAME "tiff-4.0.8.zip")
set(GLEW_FILENAME "glew-2.0.0.tgz")
set(QT5_FILENAME "qt-everywhere-opensource-src-5.5.1.7z")
set(GTEST_FILENAME "release-1.8.0.tar.gz")

set(LIBJPEG_URL "http://www.ijg.org/files/${LIBJPEG_FILENAME}")
set(LIBPNG_URL "https://download.sourceforge.net/libpng/${LIBPNG_FILENAME}")
set(ZLIB_URL "https://download.sourceforge.net/libpng/${ZLIB_FILENAME}")
set(LIBTIFF_URL "http://download.osgeo.org/libtiff/${LIBTIFF_FILENAME}")
set(GLEW_URL "https://downloads.sourceforge.net/project/glew/glew/2.0.0/${GLEW_FILENAME}")
set(QT5_URL "https://download.qt.io/archive/qt/5.5/5.5.1/single/${QT5_FILENAME}")
set(GTEST_URL "https://github.com/google/googletest/archive/${GTEST_FILENAME}")

set(PATCHES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/patches")

file(MAKE_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/bin")
file(MAKE_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/include")
file(MAKE_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/lib")

set(PFx86 "ProgramFiles(x86)")
find_file(WIN32_MAK_LOCATION win32.mak
    DOC "Location of win32.mak"
    PATHS
        "$ENV{${PFx86}}\\Microsoft SDKs\\Windows\\v7.0A\\Include"
        "$ENV{${PFx86}}\\Microsoft SDKs\\Windows\\v7.1\\Include"
        "$ENV{${PFx86}}\\Microsoft SDKs\\Windows\\v7.1A\\Include"
        ENV Path)
ExternalProject_Add(libjpeg
    PREFIX build/libjpeg
    URL ${LIBJPEG_URL}
    URL_HASH SHA256=d98f2a927bd17a58fe14781ec7957b5d514a8e5f04c0d071e65b967bf34e19d4
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CONFIGURE_COMMAND nmake /f makefile.vc setup-v10
    BUILD_COMMAND nmake /f makefile.vc
    INSTALL_COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_BINARY_DIR}/build/libjpeg/src/libjpeg/jconfig.h <INSTALL_DIR>/include
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_BINARY_DIR}/build/libjpeg/src/libjpeg/jerror.h   <INSTALL_DIR>/include
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_BINARY_DIR}/build/libjpeg/src/libjpeg/jmorecfg.h <INSTALL_DIR>/include
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_BINARY_DIR}/build/libjpeg/src/libjpeg/jpegint.h  <INSTALL_DIR>/include
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_BINARY_DIR}/build/libjpeg/src/libjpeg/jpeglib.h  <INSTALL_DIR>/include
    COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_BINARY_DIR}/build/libjpeg/src/libjpeg/libjpeg.lib  <INSTALL_DIR>/lib
    BUILD_IN_SOURCE 1)
ExternalProject_Add_Step(libjpeg copy_win32_mak
    COMMAND ${CMAKE_COMMAND} -E copy ${WIN32_MAK_LOCATION} <SOURCE_DIR>
    DEPENDEES download
    DEPENDERS configure)

ExternalProject_Add(zlib
    PREFIX build/zlib
    URL ${ZLIB_URL}
    URL_HASH SHA256=4ff941449631ace0d4d203e3483be9dbc9da454084111f97ea0a2114e19bf066
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:STRING=<INSTALL_DIR>
        -DSKIP_INSTALL_FILES:BOOL=ON)

ExternalProject_Add(libpng
    DEPENDS zlib
    PREFIX build/libpng
    URL ${LIBPNG_URL}
    URL_HASH SHA256=b09a4261ca11cd47f74bf99f6552f46ed637db19e4da976c607a2c015d252c01
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:STRING=<INSTALL_DIR>
        -DPNG_STATIC:BOOL=OFF
        -DPNG_TESTS:BOOL=OFF
        -DSKIP_INSTALL_PROGRAMS:BOOL=ON
        -DSKIP_INSTALL_FILES:BOOL=ON)

ExternalProject_Add(libtiff
    DEPENDS zlib
    PREFIX build/libtiff
    URL ${LIBTIFF_URL}
    URL_HASH SHA256=099c77cf0975a79c8425f22884fd2dc1d6ce072119e3f39f751c6f4480533e23
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR>
        -Dcxx:BOOL=OFF
        -Djpeg:BOOL=OFF
        -Dold-jpeg:BOOL=OFF)

if(CMAKE_BUILD_TYPE STREQUAL "Debug"
        OR CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
    set(VS_CONFIG "Debug")
elseif(CMAKE_BUILD_TYPE STREQUAL "Release"
        OR CMAKE_BUILD_TYPE STREQUAL "MinSizeRel"
        OR NOT CMAKE_BUILD_TYPE)
    set(VS_CONFIG "Release")
endif()

if(CMAKE_CL_64)
    set(VS_PLATFORM "x64")
else()
    set(VS_PLATFORM "Win32")
endif()

if(MSVC14)
    set(VS_PLATFORM_TOOLSET "v140")
else()
    message(FATAL_ERROR "Unsupported Visual Studio version")
endif()

ExternalProject_Add(glew
    PREFIX build/glew
    URL ${GLEW_URL}
    URL_HASH SHA256=c572c30a4e64689c342ba1624130ac98936d7af90c3103f9ce12b8a0c5736764
    SOURCE_SUBDIR build/cmake
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR>
        -DBUILD_UTILS:BOOL=OFF
        -DBUILD_SHARED_LIBS:BOOL=ON)

ExternalProject_Add(qt5
    PREFIX qt5
    URL ${QT5_URL}
    URL_HASH SHA256=eb7c430f9f73d8f9d1a0d328e8a77549ffcf3b9915bee0c3dd6ae9ceffb86ef9
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CONFIGURE_COMMAND
        cmd /C configure -prefix "<INSTALL_DIR>" -debug-and-release -opensource
            -confirm-license -shared -no-audio-backend -mp -opengl desktop
            -skip qt3d -skip qtactiveqt -skip qtandroidextras
            -skip qtcanvas3d -skip qtconnectivity -skip qtdeclarative
            -skip qtdoc -skip qtenginio -skip qtgraphicaleffects
            -skip qtlocation -skip qtmultimedia -skip qtquick1
            -skip qtquickcontrols -skip qtscript -skip qtsensors
            -skip qtserialport -skip qttools -skip qttranslations
            -skip qtwayland -skip qtwebchannel -skip qtwebkit
            -skip qtwebkit-examples -skip qtwebsockets
            -nomake examples -no-compile-examples
    BUILD_COMMAND nmake
    BUILD_IN_SOURCE 1
    INSTALL_COMMAND nmake install)

ExternalProject_Add(gtest
    PREFIX build/gtest
    URL ${GTEST_URL}
    URL_HASH SHA256=58a6f4277ca2bc8565222b3bbd58a177609e9c488e8a72649359ba51450db7d8
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR>)

