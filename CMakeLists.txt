cmake_minimum_required(VERSION 2.8.12)
project(vgui_dll)

set(VGUI_LIB vgui)

file(GLOB_RECURSE VGUI_SRC "src/vgui/*.cpp")
file(GLOB_RECURSE VGUI_WIN32_SRC "src/platform/win32/*.cpp")
file(GLOB_RECURSE VGUI_LINUX_SRC "src/platform/linux/*.cpp")

if(WIN32)
	list(APPEND VGUI_SRC ${VGUI_WIN32_SRC})
	include_directories("src/platform/win32")
else()
	list(APPEND VGUI_SRC ${VGUI_LINUX_SRC})
	include_directories("src/platform/linux")
endif()

if(NOT MSVC)
	add_definitions(-D_LINUX)
	add_definitions(-Dstricmp=strcasecmp -Dstrnicmp=strncasecmp -D_snprintf=snprintf -D_vsnprintf=vsnprintf)
	add_compile_options(-Wno-switch -Wno-implicit-exception-spec-mismatch -Wno-writable-strings)
endif()

include_directories("include")

if(ANDROID)
	add_library(${VGUI_LIB} STATIC ${VGUI_SRC})
else()
	add_library(${VGUI_LIB} SHARED ${VGUI_SRC})
endif()