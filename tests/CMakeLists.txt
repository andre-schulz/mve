enable_testing()

find_package(GTest QUIET)
if(NOT GTest_FOUND)
    message("Could not find GTest. Tests will not be built.")
    return()
endif()
include_directories(${GTEST_INCLUDE_DIR})

add_subdirectory(fssr)
add_subdirectory(math)
add_subdirectory(mve)
add_subdirectory(sfm)
add_subdirectory(util)
