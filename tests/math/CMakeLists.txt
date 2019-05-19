file (GLOB SOURCES "[^_]*.cc")

# Add test cpp file
add_executable(math_test ${SOURCES})

# Link test executable against gtest & gtest_main
target_link_libraries(math_test
    GTest::GTest
    GTest::Main
    mve_math
)

add_test(NAME math_test COMMAND math_test)
