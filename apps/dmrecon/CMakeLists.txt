file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

add_executable(dmrecon ${SOURCES} ${HEADERS})
target_link_libraries(dmrecon PRIVATE mve_dmrecon mve mve_util OpenMP::OpenMP_CXX)
install(TARGETS dmrecon RUNTIME DESTINATION .)
