file (GLOB HEADERS "*.h")
file (GLOB SOURCES "*.cc")

qt5_wrap_cpp(EXAMPLE_HEADERS_MOC ${HEADERS})

add_library(example MODULE ${SOURCES} ${EXAMPLE_HEADERS_MOC})
target_compile_definitions(example PRIVATE -DCOMPILING_PLUGIN)
target_link_libraries(example PRIVATE umve)

install(TARGETS example LIBRARY DESTINATION plugins)
