file (GLOB SOURCES "[^_]*.cc")

# Add test cpp file
add_executable(fssr_test ${SOURCES})

# Link test executable against gtest & gtest_main
target_link_libraries(fssr_test
    GTest::GTest
    GTest::Main
    mve_fssr
)

add_test(NAME fssr_test COMMAND fssr_test)
