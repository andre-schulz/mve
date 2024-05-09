cmake_minimum_required(VERSION 3.7)
if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.24)
    cmake_policy(SET CMP0135 NEW)
endif()

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

# Qt's build system requires either Perl or PowerShell >= 5.0
find_package(Perl QUIET)

# Determine PowerShell version
execute_process(COMMAND powershell -command $PSVersionTable.PSVersion.Major
    OUTPUT_VARIABLE POWERSHELL_VERSION_MAJOR OUTPUT_STRIP_TRAILING_WHITESPACE)
execute_process(COMMAND powershell -command $PSVersionTable.PSVersion.Minor
    OUTPUT_VARIABLE POWERSHELL_VERSION_MINOR OUTPUT_STRIP_TRAILING_WHITESPACE)
set(POWERSHELL_VERSION ${POWERSHELL_VERSION_MAJOR}.${POWERSHELL_VERSION_MINOR})
if(NOT PERL_FOUND AND POWERSHELL_VERSION VERSION_LESS 5.0)
    message(WARNING "Qt5 will NOT be built because neither Perl nor PowerShell >= 5.0 were found!"
        " If you don't want to build UMVE, you can ignore this warning."
        " If you want to build UMVE, please install either Perl or update your PowerShell.")
endif()

set(LIBJPEG_TURBO_FILENAME "libjpeg-turbo-3.0.3.tar.gz")
set(LIBJPEG_TURBO_URL "https://github.com/libjpeg-turbo/libjpeg-turbo/releases/download/3.0.3/${LIBJPEG_TURBO_FILENAME}")
set(LIBJPEG_TURBO_SHA256 "343e789069fc7afbcdfe44dbba7dbbf45afa98a15150e079a38e60e44578865d")

set(LIBPNG_FILENAME "libpng-1.6.40.tar.xz")
set(LIBPNG_URL "https://download.sourceforge.net/libpng/libpng16/1.6.40/${LIBPNG_FILENAME}")
set(LIBPNG_SHA256 "535b479b2467ff231a3ec6d92a525906fb8ef27978be4f66dbe05d3f3a01b3a1")

set(ZLIB_FILENAME "zlib-1.3.1.tar.xz")
set(ZLIB_URL "https://www.zlib.net/${ZLIB_FILENAME}")
set(ZLIB_SHA256 "38ef96b8dfe510d42707d9c781877914792541133e1870841463bfa73f883e32")

set(LIBTIFF_FILENAME "tiff-4.6.0.tar.xz")
set(LIBTIFF_URL "https://download.osgeo.org/libtiff/${LIBTIFF_FILENAME}")
set(LIBTIFF_SHA256 "e178649607d1e22b51cf361dd20a3753f244f022eefab1f2f218fc62ebaf87d2")

set(GLEW_FILENAME "glew-2.2.0.tgz")
set(GLEW_URL "https://downloads.sourceforge.net/project/glew/glew/2.2.0/${GLEW_FILENAME}")
set(GLEW_SHA256 "d4fc82893cfb00109578d0a1a2337fb8ca335b3ceccf97b97e5cc7f08e4353e1")

set(QT5_FILENAME "qt-everywhere-opensource-src-5.15.11.tar.xz")
set(QT5_URL "https://download.qt.io/official_releases/qt/5.15/5.15.11/single/${QT5_FILENAME}")
set(QT5_SHA256 "7426b1eaab52ed169ce53804bdd05dfe364f761468f888a0f15a308dc1dc2951")

set(GTEST_FILENAME "release-1.12.1.tar.gz")
set(GTEST_URL "https://github.com/google/googletest/archive/${GTEST_FILENAME}")
set(GTEST_SHA256 "81964fe578e9bd7c94dfdb09c8e4d6e6759e19967e397dbea48d1c10e45d0df2")

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
        -Dtiff-contrib:BOOL=OFF
        -Dtiff-docs:BOOL=OFF
        -Dtiff-tests:BOOL=OFF
        -Dtiff-tools:BOOL=OFF
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

if(PERL_FOUND OR POWERSHELL_VERSION VERSION_GREATER_EQUAL 5.0)
    ExternalProject_Add(qt5
        PREFIX build/qt5
        URL ${QT5_URL}
        URL_HASH SHA256=${QT5_SHA256}
        INSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}
        CONFIGURE_COMMAND
            cmd /C configure -prefix "<INSTALL_DIR>" -debug-and-release -opensource
                -confirm-license -shared -mp -opengl desktop
                -nomake examples -nomake tests
                -no-accessibility -no-dbus -no-feature-network
                -no-feature-printer -no-feature-sql -no-feature-testlib
                -skip qt3d -skip qtactiveqt -skip qtandroidextras
                -skip qtcharts -skip qtconnectivity
                -skip qtdatavis3d -skip qtdeclarative -skip qtdoc -skip qtgamepad
                -skip qtgraphicaleffects -skip qtlocation -skip qtlottie
                -skip qtmacextras -skip qtmultimedia -skip qtnetworkauth
                -skip qtpurchasing -skip qtquick3d -skip qtquickcontrols -skip quickcontrols2
                -skip qtquicktimeline -skip qtremoteobjects -skip qtscript
                -skip qtscxml -skip qtsensors -skip qtserialbus -skip qtserialport
                -skip qtspeech -skip qttools -skip qttranslations
                -skip qtvirtualkeyboard -skip qtwayland -skip qtwebchannel
                -skip qtwebengine -skip qtwebglplugin -skip qtwebsockets
                -skip qtwebview -skip qtx11extras -skip qtxmlpatterns
        BUILD_COMMAND nmake
        BUILD_IN_SOURCE 1
        INSTALL_COMMAND nmake install
    )
endif()

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

