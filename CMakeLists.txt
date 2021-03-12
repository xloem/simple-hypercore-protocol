cmake_minimum_required(VERSION 3.10)

project(simple-hypercore-protocol)

execute_process(COMMAND git submodule update --init --recursive WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

#add_subdirectory(deps/datcxx/timers)
#add_subdirectory(deps/datcxx/cxx-eventemitter)
include(ExternalProject)

ExternalProject_Add(
    noise-c-autoconf SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/deps/rweather/noise-c
    PATCH_COMMAND ./autogen.sh CONFIGURE_COMMAND <SOURCE_DIR>/configure --prefix=<INSTALL_DIR>
    BUILD_COMMAND make INSTALL_COMMAND make install TEST_COMMAND make check
)
# possible resource: https://stackoverflow.com/questions/49203933
add_library(${PROJECT_NAME} INTERFACE)
#target_link_libraries(${PROJECT_NAME} INTERFACE noise-c)

target_include_directories(${PROJECT_NAME} INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/..)
install(FILES index.hxx DESTINATION include/${PROJECT_NAME})

include(CTest)
if(BUILD_TESTING)
	add_executable(${PROJECT_NAME}-test test.cxx)
	target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
	add_test(NAME ${PROJECT_NAME} COMMAND ${PROJECT_NAME}-test)
endif()
