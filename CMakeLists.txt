cmake_minimum_required(VERSION 3.1)

project(MVE)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED yes)

# Warning flags
if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    string(APPEND CMAKE_CXX_FLAGS " /W3")
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    string(APPEND CMAKE_CXX_FLAGS " -Wall -Wextra -Wundef -pedantic")
    string(APPEND CMAKE_CXX_FLAGS " -mpopcnt")
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    string(APPEND CMAKE_CXX_FLAGS " -Weverything -pedantic")
endif()

if(WIN32)
    add_definitions(-DWIN32_LEAN_AND_MEAN -DVC_EXTRALEAN -DNOMINMAX)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    if(MSVC)
        string(APPEND CMAKE_CXX_FLAGS " /MP")
        option(MVE_BUILD_WITH_SSE2 "Build MVE with SSE2 support" ON)
        option(MVE_BUILD_WITH_SSE3 "Build MVE with SSE3 support" ON)
        if(MVE_BUILD_WITH_SSE2)
            add_definitions(-D__SSE2__)
        endif()
        if(MVE_BUILD_WITH_SSE3)
            add_definitions(-D__SSE3__)
        endif()
    endif()

    set(CMAKE_PREFIX_PATH "${CMAKE_CURRENT_SOURCE_DIR}/3rdparty")
    set(JPEG_NAMES "libjpeg")
    set(CPACK_GENERATOR "ZIP")
else()
    set(CPACK_GENERATOR "TGZ")
endif()
include(CPack)

find_package(OpenMP REQUIRED)
string(APPEND CMAKE_CXX_FLAGS " ${OpenMP_CXX_FLAGS}")

include_directories(libs)

add_subdirectory(libs)
add_subdirectory(apps)
#add_subdirectory(tests)

# Install 3rd party libraries
if(WIN32)
    set(LIBPNG_DLL_DEBUG "3rdparty/bin/libpng16d.dll")
    set(LIBPNG_DLL_RELEASE "3rdparty/bin/libpng16.dll")
    set(ZLIB_DLL_RELEASE "3rdparty/bin/zlib.dll")
    set(LIBTIFF_DLL_RELEASE "3rdparty/bin/tiff.dll")
    set(GLEW_DLL_RELEASE "3rdparty/bin/glew32.dll")
    install(FILES ${LIBPNG_DLL_RELEASE} ${ZLIB_DLL_RELEASE}
        ${LIBTIFF_DLL_RELEASE} ${GLEW_DLL_RELEASE}
        DESTINATION . CONFIGURATIONS Release RelWithDebInfo)
    install(FILES ${LIBPNG_DLL_DEBUG} ${ZLIB_DLL_RELEASE}
        ${LIBTIFF_DLL_RELEASE} DESTINATION . CONFIGURATIONS Debug)
endif()

