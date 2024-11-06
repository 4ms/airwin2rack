cmake_minimum_required(VERSION 3.22)

if ("${METAMODULE_SDK_DIR}" STREQUAL "")
  if ("$ENV{METAMODULE_SDK_DIR}" STREQUAL "")
	message(FATAL_ERROR "Path to MetaModule SDK is missing! Add -DMETAMODULE_SDK_DIR=<PATH> to the cmake call or as an environment variable.")
  else()
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
  endif()
else()
  message(STATUS "Using MetaModule-SDK in '${METAMODULE_SDK_DIR}'")
endif()

include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(AirwindowsModules 
    VERSION 0.1 
    DESCRIPTION "Airwindows Plugin for MetaModule" 
    LANGUAGES C CXX ASM
)

add_library(AirwindowsModules STATIC)


target_include_directories(AirwindowsModules PRIVATE 
	. 
    ${CMAKE_CURRENT_LIST_DIR}/../src
)

target_sources(AirwindowsModules PRIVATE
	airwin_modules.cc
)

target_compile_definitions(AirwindowsModules PRIVATE 
	DISABLE_CONSOLE
)

add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/../ ${CMAKE_CURRENT_BINARY_DIR}/airwin2rack)
target_compile_options(airwin-registry PRIVATE "-Wno-misleading-indentation;-Wno-unused-variable;-Wno-stringop-truncation")

target_link_libraries(AirwindowsModules PUBLIC airwin-registry)
set(EXTRA_LINK_OPTIONS $<TARGET_OBJECTS:airwin-registry>)

create_plugin(
    SOURCE_LIB      AirwindowsModules
    PLUGIN_NAME     Airwindows
    PLUGIN_JSON     ${CMAKE_CURRENT_LIST_DIR}/../plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugins
)

