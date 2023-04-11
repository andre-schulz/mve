file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

add_library(mve_fssr STATIC ${SOURCES} ${HEADERS})
target_link_libraries(mve_fssr
    PUBLIC
        mve
        mve_math
    PRIVATE
        mve_util
        OpenMP::OpenMP_CXX
)
