file (GLOB HEADERS "*.h")
file (GLOB SOURCES "*.cc")

add_executable(makescene ${SOURCES} ${HEADERS})
target_link_libraries(makescene PRIVATE mve mve_util OpenMP::OpenMP_CXX)
install(TARGETS makescene RUNTIME DESTINATION .)
