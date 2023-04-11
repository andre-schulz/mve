file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

find_package(JPEG REQUIRED)
find_package(PNG REQUIRED)
find_package(TIFF REQUIRED)

add_library(mve STATIC ${SOURCES} ${HEADERS})
target_link_libraries(mve
    PUBLIC
        mve_math
        mve_util
    PRIVATE
        JPEG::JPEG
        PNG::PNG
        TIFF::TIFF
)
