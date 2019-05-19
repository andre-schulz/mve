file (GLOB SOURCES "[^_]*.cc")

# Add test cpp file
add_executable(sfm_test ${SOURCES})

# Link test executable against gtest & gtest_main
target_link_libraries(sfm_test
    GTest::GTest
    GTest::Main
    mve_sfm
)

add_test(NAME sfm_test COMMAND sfm_test)
