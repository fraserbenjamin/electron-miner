# Copyright (C) 1883 Thomas Edison - All Rights Reserved
# You may use, distribute and modify this code under the
# terms of the GPLv3 license, which unfortunately won't be
# written for another century.
#
# You should have received a copy of the LICENSE file with
# this file. 

cmake_policy(SET CMP0015 NEW)

aux_source_directory(. SRC_LIST)

include_directories(BEFORE ..)
include_directories(${CMAKE_JS_INC})

# set(EXECUTABLE nsfminer)

file(GLOB HEADERS "*.h" "main.cpp")
#file(GLOB HEADERS "*.h" "hello.cc")

# add_executable(${EXECUTABLE} ${SRC_LIST} ${HEADERS})
# if(MSVC)
# 	target_sources(${EXECUTABLE} PRIVATE nsfminer.rc)
# endif()

hunter_add_package(Boost COMPONENTS program_options)
find_package(Boost CONFIG REQUIRED COMPONENTS program_options)

add_library(${PROJECT_NAME} SHARED ${HEADERS} ${CMAKE_JS_SRC})

# Gives our library file a .node extension without any "lib" prefix
set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "" SUFFIX ".node")

target_link_libraries(nsfminer PRIVATE
        eth pool dev nsfminer-buildinfo Boost::system Boost::thread Boost::program_options ${CMAKE_JS_LIB})

# Include Node-API wrappers
execute_process(COMMAND node -p "require('node-addon-api').include"
WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
OUTPUT_VARIABLE NODE_ADDON_API_DIR
)
string(REGEX REPLACE "[\r\n\"]" "" NODE_ADDON_API_DIR ${NODE_ADDON_API_DIR})

# Essential include files to build a node addon,
# You should add this line in every CMake.js based project
target_include_directories(${PROJECT_NAME} PRIVATE ${NODE_ADDON_API_DIR})

add_definitions(-DNAPI_VERSION=3)

# if(ETHDBUS)
# 	find_package(PkgConfig)
# 	set( ENV{PKG_CONFIG_PATH} "$ENV{PKG_CONFIG_PATH}:/usr/lib/x86_64-linux-gnu/pkgconfig" )
# 	pkg_check_modules(DBUS dbus-1)
# 	include_directories(${DBUS_INCLUDE_DIRS})
# 	link_directories(${DBUS_LIBRARY_DIRS})
# 	target_link_libraries(nsfminer PRIVATE ${DBUS_LIBRARIES})
# endif()

# if(APICORE)
# 	target_link_libraries(nsfminer PRIVATE api)
# endif()

# include(GNUInstallDirs)
# install(TARGETS nsfminer DESTINATION ${CMAKE_INSTALL_BINDIR})
# if(MSVC)
#     install(FILES $<TARGET_PDB_FILE:nsfminer> DESTINATION ${CMAKE_INSTALL_BINDIR} OPTIONAL)
# endif()
