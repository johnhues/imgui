cmake_minimum_required(VERSION 3.10 FATAL_ERROR)
project(imgui LANGUAGES CXX)
include(ExternalProject)

# C++ settings
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_definitions(-D_UNICODE -DUNICODE)

find_package(OpenGL REQUIRED)

if(WIN32 AND NOT CYGWIN)
	FetchContent_Declare(
		glew_inc
		URL https://github.com/nigels-com/glew/releases/download/glew-2.1.0/glew-2.1.0-win32.zip
	)
	FetchContent_MakeAvailable(glew_inc)
	FetchContent_GetProperties(glew_inc SOURCE_DIR glew_inc_SOURCE_DIR)
	set(GLEW_INCLUDE_DIRS ${glew_inc_SOURCE_DIR}/include)
endif()

add_library(imgui)
if(${DEPS_TARGETS})
	add_dependencies(imgui ${DEPS_TARGETS})
endif()

if(WIN32 AND NOT CYGWIN)
	add_definitions(-DIMGUI_IMPL_OPENGL_LOADER_GLEW)
elseif(APPLE)
	add_definitions("-DIMGUI_IMPL_OPENGL_LOADER_CUSTOM=<OpenGL/gl3.h>")
elseif(UNIX)
	add_definitions("-DIMGUI_IMPL_OPENGL_LOADER_CUSTOM=<GLES3/gl3.h>")
endif()

target_include_directories(imgui
	PUBLIC
		${CMAKE_CURRENT_SOURCE_DIR}
		${CMAKE_CURRENT_SOURCE_DIR}/backends
	PRIVATE
		${OPENGL_INCLUDE_DIR}
		${GLEW_INCLUDE_DIRS}
)

target_sources(imgui
	PRIVATE
		imgui.cpp
		imgui_demo.cpp
		imgui_draw.cpp
		imgui_tables.cpp
		imgui_widgets.cpp
		backends/imgui_impl_opengl3.cpp
	INTERFACE
		${CMAKE_CURRENT_SOURCE_DIR}/imgui.h
		${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.h
)
