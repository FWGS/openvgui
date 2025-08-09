cmake_minimum_required(VERSION 3.10)
project(vgui_dll)

option(VGUI_ALLOW_MALLOC_OVERRIDE "Enables global memory alloc operators replacement and vgui_setMalloc/vgui_setFree calls, like original VGUI" OFF)
option(VGUI_STATIC "Compile vgui as static library, required for mods that integrate vgui_support" OFF)

file(GLOB_RECURSE VGUI_SRC "src/vgui/*.cpp")
file(GLOB_RECURSE VGUI_WIN32_SRC "src/platform/win32/*.cpp")
file(GLOB_RECURSE VGUI_POSIX_SRC "src/platform/posix/*.cpp")
file(GLOB_RECURSE VGUI_APPLE_SRC "src/platform/apple/*.cpp")

if(WIN32)
	list(APPEND VGUI_SRC ${VGUI_WIN32_SRC})
	include_directories("src/platform/posix" "src/platform/win32")

	list(REMOVE_ITEM VGUI_POSIX_SRC
			"${CMAKE_CURRENT_SOURCE_DIR}/src/platform/posix/App.cpp"
			"${CMAKE_CURRENT_SOURCE_DIR}/src/platform/posix/Font.cpp"
			"${CMAKE_CURRENT_SOURCE_DIR}/src/platform/posix/Surface.cpp"
	)
	list(APPEND VGUI_SRC ${VGUI_POSIX_SRC})
else()
	list(APPEND VGUI_SRC ${VGUI_POSIX_SRC})
	include_directories("src/platform/posix")

	if(APPLE)
		list(APPEND VGUI_SRC ${VGUI_APPLE_SRC})
		include_directories("src/platform/apple")
	endif()
endif()

list(APPEND VGUI_SRC "miniutl/utlmemory.cpp")

if(NOT MSVC)
#	add_definitions(-DNDEBUG)
	add_definitions(-Dstricmp=strcasecmp -D_vsnprintf=vsnprintf)
#	add_compile_options(-Wno-switch -Wno-implicit-exception-spec-mismatch -Wno-writable-strings)
endif()

include_directories("include")
include_directories("miniutl")

if(VGUI_STATIC)
	add_library(vgui STATIC ${VGUI_SRC})
else()
	add_library(vgui SHARED ${VGUI_SRC})

	install(TARGETS ${VGUI_LIB}
			DESTINATION ".")

	if(MSVC)
		install(FILES $<TARGET_PDB_FILE:${VGUI_LIB}>
				DESTINATION "." OPTIONAL)
	endif()
endif()
