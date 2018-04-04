file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

add_library(mve_dmrecon STATIC ${SOURCES} ${HEADERS})
target_link_libraries(mve_dmrecon
    PUBLIC
        mve
        mve_math
    PRIVATE
        mve_util
)
