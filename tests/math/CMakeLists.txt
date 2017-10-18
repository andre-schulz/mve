file (GLOB SOURCES "[^_]*.cc")

# Add test cpp file
add_executable(math_test ${SOURCES})

# Link test executable against gtest & gtest_main
target_link_libraries(math_test ${GTEST_BOTH_LIBRARIES} mve)

add_test(NAME math_test COMMAND math_test)
