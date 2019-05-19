file (GLOB SOURCES "[^_]*.cc")

# Add test cpp file
add_executable(util_test ${SOURCES})

# Link test executable against gtest & gtest_main
target_link_libraries(util_test
    GTest::GTest
    GTest::Main
    mve_util
)

add_test(NAME util_test COMMAND util_test)
