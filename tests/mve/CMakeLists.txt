file (GLOB SOURCES "[^_]*.cc")

# Add test cpp file
add_executable(mve_test ${SOURCES})

# Link test executable against gtest & gtest_main
target_link_libraries(mve_test ${GTEST_BOTH_LIBRARIES} mve mve_util)

add_test(NAME mve_test COMMAND mve_test)
