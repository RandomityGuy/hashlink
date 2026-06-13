if(WIN32)
    set(SDL2_PATH ${INCLUDES_BASE_DIR}/sdl)
endif()
find_package(SDL2 REQUIRED)

if(CMAKE_SYSTEM_NAME STREQUAL "iOS")
    add_library(sdl.hdll STATIC sdl.c gl.c)
else()
    add_library(sdl.hdll SHARED sdl.c gl.c)
endif()
set_as_hdll(sdl)
target_include_directories(sdl.hdll
    PRIVATE
    ${SDL2_INCLUDE_DIR}
    ${INCLUDES_BASE_DIR}/gl
)
target_link_libraries(sdl.hdll
    libhl
    ${SDL2_LIBRARY}
)

if(WIN32)
    target_link_libraries(sdl.hdll
        winmm
        opengl32
    )
endif()

if(APPLE OR UNIX)
    if(CMAKE_SYSTEM_NAME STREQUAL "iOS")
        find_library(OPENGL_LIBRARIES OpenGLES REQUIRED)
        target_compile_definitions(sdl.hdll PRIVATE HL_IOS)
        target_link_libraries(sdl.hdll
            libhl
            ${OPENGL_LIBRARIES}
            "-framework AVFoundation"
            "-framework AudioToolbox"
            "-framework CoreAudio"
            "-framework CoreBluetooth"
            "-framework CoreFoundation"
            "-framework CoreGraphics"
            "-framework CoreHaptics"
            "-framework CoreMotion"
            "-framework Foundation"
            "-framework GameController"
            "-framework Metal"
            "-framework QuartzCore"
            "-framework UIKit"
            "-liconv"
        )
    else()
        find_package(OpenGL REQUIRED)
        target_include_directories(sdl.hdll
            PRIVATE
            ${OPENGL_INCLUDE_DIR}
        )
        target_link_libraries(sdl.hdll
            libhl
            ${OPENGL_gl_LIBRARY}
        )
    endif()
endif()



install(
    TARGETS sdl.hdll
    DESTINATION ${HDLL_DESTINATION}
)
