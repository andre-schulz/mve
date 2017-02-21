file (GLOB HEADERS "*.h")
file (GLOB SOURCES "*.cc")

add_executable(bundle2pset ${SOURCES} ${HEADERS})
target_link_libraries(bundle2pset PRIVATE mve mve_util)
install(TARGETS bundle2pset RUNTIME DESTINATION .)
