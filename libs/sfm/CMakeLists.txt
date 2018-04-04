file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

add_library(mve_sfm STATIC ${SOURCES} ${HEADERS})
target_link_libraries(mve_sfm
    PUBLIC
        mve
        mve_math
        mve_util
)

