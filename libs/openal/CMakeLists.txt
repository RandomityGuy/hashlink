add_library(openal.hdll SHARED openal.c)

if(WIN32)
    if(MSVC)
        set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /SAFESEH:NO")
    endif()

    if(CMAKE_SIZEOF_VOID_P EQUAL 8)
		find_library(OPENAL_LIBRARY OpenAL32 PATHS ${INCLUDES_BASE_DIR}/openal/libs/Win64)
    else()
		find_library(OPENAL_LIBRARY OpenAL32 PATHS ${INCLUDES_BASE_DIR}/openal/libs/Win32)
    endif()



    target_include_directories(openal.hdll
        PRIVATE
        ${INCLUDES_BASE_DIR}/openal/include
    )


else()
	find_package(OpenAL)
	target_compile_definitions(openal.hdll
		PRIVATE
		CMAKE_BUILD
	)
	target_include_directories(openal.hdll
		PRIVATE
		${OPENAL_INCLUDE_DIR}
	)
endif()

set_as_hdll(openal)
target_link_libraries(openal.hdll
    libhl
    ${OPENAL_LIBRARY}
)

install(
    TARGETS
        openal.hdll
    DESTINATION ${HDLL_DESTINATION}
)
