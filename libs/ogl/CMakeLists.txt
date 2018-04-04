file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

find_package(OpenGL REQUIRED)
if(WIN32)
    find_package(GLEW REQUIRED)
endif()

add_library(mve_ogl STATIC ${SOURCES} ${HEADERS})
target_include_directories(mve_ogl PUBLIC ${OPENGL_INCLUDE_DIR})

target_link_libraries(mve_ogl
    PUBLIC
        mve
        mve_math
        mve_util
        ${OPENGL_LIBRARIES}
)
if(WIN32)
    target_link_libraries(mve_ogl PUBLIC GLEW::GLEW)
endif()

