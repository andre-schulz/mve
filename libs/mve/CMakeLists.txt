file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

find_package(JPEG REQUIRED)
find_package(PNG REQUIRED)
find_package(TIFF REQUIRED)

add_library(mve STATIC ${SOURCES} ${HEADERS})
target_include_directories(mve PRIVATE ${JPEG_INCLUDE_DIR})
target_link_libraries(mve
    PUBLIC
        mve_math
        mve_util
    PRIVATE
        ${JPEG_LIBRARIES}
        PNG::PNG
        TIFF::TIFF
)
