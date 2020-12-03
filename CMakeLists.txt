## This is the CMake script that our main build pipeline uses to compile the library

cmake_minimum_required(VERSION 3.12)

##################################################
## Project
##################################################
set(CURRENT_TARGET_NAME "imgui-node-editor")
project(${CURRENT_TARGET_NAME})

# Node editor use C++14
set(CMAKE_CXX_STANDARD            14)
set(CMAKE_CXX_STANDARD_REQUIRED   YES)


# Define IMGUI_NODE_EDITOR_ROOT_DIR pointing to project root directory
get_filename_component(IMGUI_NODE_EDITOR_ROOT_DIR ${CMAKE_SOURCE_DIR} ABSOLUTE CACHE)

# Point CMake where to look for module files.
list(APPEND CMAKE_MODULE_PATH ${IMGUI_NODE_EDITOR_ROOT_DIR}/misc/cmake-modules)

# Enable solution folders in Visual Studio and Folders in Xcode
set_property(GLOBAL PROPERTY USE_FOLDERS ON)


##################################################
## Includes
##################################################
include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
	${CMAKE_CURRENT_SOURCE_DIR}/external/imgui
	${CMAKE_CURRENT_SOURCE_DIR}/external/gl3w
	${CMAKE_CURRENT_SOURCE_DIR}/external/stb_image
	${CMAKE_CURRENT_SOURCE_DIR}/external/ScopeGuard
)	# The current source directory


##################################################
## Source codes
##################################################
set(CURRENT_SRC
	crude_json.cpp
	imgui_canvas.cpp
	imgui_node_editor.cpp
	imgui_node_editor_api.cpp
)


##################################################
## Shared libraries
##################################################
set(CURRENT_SHARED_LIBRARIES
	# Not required in this project
)


##################################################
## Build
##################################################
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x -ffor-scope -fno-rtti -fno-exceptions -pipe -ffunction-sections -fdata-sections -ffast-math -Wnon-virtual-dtor -Wreorder -Wsign-promo -fvisibility=hidden -fvisibility-inlines-hidden -Wstrict-null-sentinel -Os -funroll-all-loops -fpeel-loops -ftree-vectorize")
set(LINKER_FLAGS "${LINKER_FLAGS} -Wl,--as-needed -Wl,--gc-sections -Wl,--no-undefined -Wl,--strip-all")
if(ANDROID)
	set(LINKER_FLAGS "${LINKER_FLAGS} -Wl,-rpath-link=${ANDROID_NDK_SYSROOT}/usr/lib/ -L${ANDROID_NDK_SYSROOT}/usr/lib/")
endif()
add_library(${CURRENT_TARGET_NAME} STATIC ${CURRENT_SRC})
target_link_libraries(${CURRENT_TARGET_NAME} ${CURRENT_SHARED_LIBRARIES})
set_target_properties(${CURRENT_TARGET_NAME} PROPERTIES COMPILE_DEFINITIONS "__STDC_INT64__")

#if(WIN32)
#	add_custom_command(
#		TARGET ${CURRENT_TARGET_NAME}
#		PRE_BUILD
#		COMMAND ${CMAKE_COMMAND}
#		ARGS -E copy ${CMAKE_CURRENT_SOURCE_DIR}/jconfig.vc ${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h
#	)
#else()
#	add_custom_command(
#		TARGET ${CURRENT_TARGET_NAME}
#		PRE_BUILD
#		COMMAND ${CMAKE_COMMAND}
#		ARGS -E copy ${CMAKE_CURRENT_SOURCE_DIR}/jconfig.vc ${CMAKE_CURRENT_SOURCE_DIR}/jconfig.h
#	)
#endif()

##################################################
## Install
##################################################
install(TARGETS ${CURRENT_TARGET_NAME}
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
)

install(FILES
	${CMAKE_CURRENT_SOURCE_DIR}/crude_json.h
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_bezier_math.h
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_bezier_math.inl
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_canvas.h
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_extra_math.h
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_extra_math.inl
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_node_editor.h
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_node_editor_internal.h
	${CMAKE_CURRENT_SOURCE_DIR}/imgui_node_editor_internal.inl
	DESTINATION include
)

add_subdirectory(external/gl3w)
add_subdirectory(external/imgui)
add_subdirectory(external/ScopeGuard)
add_subdirectory(external/stb_image)
add_subdirectory(examples)
