cmake_minimum_required(VERSION 3.7)
project(violet
	VERSION 0.1.0
	LANGUAGES C)

set(PROJECT_DESCRIPTION "Lightweight STUN/TURN server")

# Options
option(WARNINGS_AS_ERRORS "Treat warnings as errors" OFF)

set(C_STANDARD 11)

if(WIN32)
	add_definitions(-DWIN32_LEAN_AND_MEAN)
	if(MSVC)
		add_definitions(-DNOMINMAX)
		add_definitions(-D_CRT_SECURE_NO_WARNINGS)
		add_definitions(-D_SILENCE_CXX17_CODECVT_HEADER_DEPRECATION_WARNING)
	endif()
endif()

set(VIOLET_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/src/main.c
	${CMAKE_CURRENT_SOURCE_DIR}/src/options.c
)

set(VIOLET_HEADERS

)

add_executable(violet ${VIOLET_SOURCES})

option(NO_TESTS "Disable tests for libjuice" ON)
option(NO_EXAMPLES "Disable examples for libjuice" ON)
add_subdirectory(deps/libjuice EXCLUDE_FROM_ALL)
target_link_libraries(violet PRIVATE LibJuice::LibJuiceStatic)

install(TARGETS violet LIBRARY DESTINATION bin)

if(NOT MSVC)
	target_compile_options(violet PRIVATE -Wall -Wextra)
endif()

if(WARNINGS_AS_ERRORS)
	if(MSVC)
		target_compile_options(violet PRIVATE /WX)
	else()
		target_compile_options(violet PRIVATE -Werror)
	endif()
endif()
