file (GLOB HEADERS "*.h")
file (GLOB SOURCES "*.cc")

add_executable(sfmrecon ${SOURCES} ${HEADERS})
target_link_libraries(sfmrecon PRIVATE mve mve_sfm mve_util OpenMP::OpenMP_CXX)
install(TARGETS sfmrecon RUNTIME DESTINATION .)
