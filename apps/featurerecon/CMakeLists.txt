file (GLOB HEADERS "*.h")
file (GLOB SOURCES "*.cc")

add_executable(featurerecon ${SOURCES} ${HEADERS})
target_link_libraries(featurerecon PRIVATE mve mve_math mve_sfm mve_util)
install(TARGETS featurerecon RUNTIME DESTINATION .)
