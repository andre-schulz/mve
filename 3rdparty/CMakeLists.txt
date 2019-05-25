cmake_minimum_required(VERSION 3.7)
set(CMAKE_CONFIGURATION_TYPES Release)

project(3rdparty)
include(ExternalProject)

if(NOT WIN32)
    message(FATAL_ERROR "The 3rdparty project has to be built only on Windows")
endif()

if(NOT MSVC)
    message(FATAL_ERROR "Compilers other than Visual Studio are unsupported on Windows")
endif()

if(NOT CMAKE_CL_64)
    message(FATAL_ERROR "32-bit builds are unsupported")
endif()

set(LIBJPEG_TURBO_FILENAME "libjpeg-turbo-2.0.2.tar.gz")
set(LIBJPEG_TURBO_URL "https://download.sourceforge.net/project/libjpeg-turbo/2.0.2/${LIBJPEG_TURBO_FILENAME}")
set(LIBJPEG_TURBO_SHA256 "acb8599fe5399af114287ee5907aea4456f8f2c1cc96d26c28aebfdf5ee82fed")

set(LIBPNG_FILENAME "lpng1637.7z")
set(LIBPNG_URL "https://download.sourceforge.net/libpng/${LIBPNG_FILENAME}")
set(LIBPNG_SHA256 "1f1e8fc10d3575c9694a72d63c49752d2251d9683b6030850d806acc5e5e86fa")

set(ZLIB_FILENAME "zlib-1.2.11.tar.xz")
set(ZLIB_URL "https://download.sourceforge.net/libpng/${ZLIB_FILENAME}")
set(ZLIB_SHA256 "4ff941449631ace0d4d203e3483be9dbc9da454084111f97ea0a2114e19bf066")

set(LIBTIFF_FILENAME "tiff-4.0.10.tar.gz")
set(LIBTIFF_URL "http://download.osgeo.org/libtiff/${LIBTIFF_FILENAME}")
set(LIBTIFF_SHA256 "2c52d11ccaf767457db0c46795d9c7d1a8d8f76f68b0b800a3dfe45786b996e4")

set(GLEW_FILENAME "glew-2.1.0.tgz")
set(GLEW_URL "https://downloads.sourceforge.net/project/glew/glew/2.1.0/${GLEW_FILENAME}")
set(GLEW_SHA256 "04de91e7e6763039bc11940095cd9c7f880baba82196a7765f727ac05a993c95")

set(QT5_FILENAME "qt-everywhere-opensource-src-5.6.3.tar.xz")
set(QT5_URL "https://download.qt.io/archive/qt/5.6/5.6.3/single/${QT5_FILENAME}")
set(QT5_SHA256 "2fa0cf2e5e8841b29a4be62062c1a65c4f6f2cf1beaf61a5fd661f520cd776d0")

set(GTEST_FILENAME "release-1.8.1.tar.gz")
set(GTEST_URL "https://github.com/google/googletest/archive/${GTEST_FILENAME}")
set(GTEST_SHA256 "9bf1fe5182a604b4135edc1a425ae356c9ad15e9b23f9f12a02e80184c3a249c")

set(PATCHES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/patches")

file(MAKE_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/bin")
file(MAKE_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/include")
file(MAKE_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/lib")

ExternalProject_Add(libjpeg-turbo
    PREFIX build/libjpeg-turbo
    URL ${LIBJPEG_TURBO_URL}
    URL_HASH SHA256=${LIBJPEG_TURBO_SHA256}
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_DEBUG_POSTFIX:STRING=d
        -DCMAKE_INSTALL_PREFIX:STRING=<INSTALL_DIR>
        -DENABLE_STATIC:BOOL=OFF
        -DWITH_SIMD:BOOL=OFF
        -DWITH_TURBOJPEG:BOOL=OFF
)

ExternalProject_Add(zlib
    PREFIX build/zlib
    URL ${ZLIB_URL}
    URL_HASH SHA256=${ZLIB_SHA256}
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:STRING=<INSTALL_DIR>
        -DSKIP_INSTALL_FILES:BOOL=ON
)

ExternalProject_Add(libpng
    DEPENDS zlib
    PREFIX build/libpng
    URL ${LIBPNG_URL}
    URL_HASH SHA256=${LIBPNG_SHA256}
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:STRING=<INSTALL_DIR>
        -DPNG_STATIC:BOOL=OFF
        -DPNG_TESTS:BOOL=OFF
        -DSKIP_INSTALL_PROGRAMS:BOOL=ON
        -DSKIP_INSTALL_FILES:BOOL=ON
)

ExternalProject_Add(libtiff
    DEPENDS zlib
    PREFIX build/libtiff
    URL ${LIBTIFF_URL}
    URL_HASH SHA256=${LIBTIFF_SHA256}
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR>
        -Dcxx:BOOL=OFF
        -Djpeg:BOOL=OFF
        -Dold-jpeg:BOOL=OFF
)

ExternalProject_Add(glew
    PREFIX build/glew
    URL ${GLEW_URL}
    URL_HASH SHA256=${GLEW_SHA256}
    SOURCE_SUBDIR build/cmake
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR>
        -DBUILD_UTILS:BOOL=OFF
        -DBUILD_SHARED_LIBS:BOOL=ON
)

ExternalProject_Add(qt5
    PREFIX qt5
    URL ${QT5_URL}
    URL_HASH SHA256=${QT5_SHA256}
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CONFIGURE_COMMAND
        cmd /C configure -prefix "<INSTALL_DIR>" -debug-and-release -opensource
            -confirm-license -shared -no-audio-backend -mp -opengl desktop
            -skip qt3d -skip qtactiveqt -skip qtandroidextras
            -skip qtcanvas3d -skip qtconnectivity -skip qtdeclarative
            -skip qtdoc -skip qtenginio -skip qtgraphicaleffects
            -skip qtlocation -skip qtmacextras -skip qtmultimedia
            -skip qtquickcontrols -skip quickcontrols2 -skip qtscript
            -skip qtsensors -skip qtserialbus -skip qtserialport -skip qttools
            -skip qttranslations -skip qtwayland -skip qtwebchannel
            -skip qtwebengine -skip qtwebsockets -skip qtwebview
            -skip qtx11extras -nomake examples -no-compile-examples
    BUILD_COMMAND nmake
    BUILD_IN_SOURCE 1
    INSTALL_COMMAND nmake install
)

ExternalProject_Add(gtest
    PREFIX build/gtest
    URL ${GTEST_URL}
    URL_HASH SHA256=${GTEST_SHA256}
    INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CMAKE_ARGS
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
        -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR>
        -DBUILD_GMOCK:BOOL=OFF
        -Dgtest_force_shared_crt:BOOL=ON
        -Dgtest_hide_internal_symbols:BOOL=ON
)

