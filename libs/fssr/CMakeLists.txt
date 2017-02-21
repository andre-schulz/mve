file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

add_library(mve_fssr STATIC ${SOURCES} ${HEADERS})
target_link_libraries(mve_fssr
    PRIVATE
        mve
        mve_math
        mve_util
)
