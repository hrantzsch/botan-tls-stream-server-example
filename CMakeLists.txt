cmake_minimum_required(VERSION 3.12.4)

set(PROJECT_NAME "botan-tls-stream-server")
project(${PROJECT_NAME})

find_package(Boost 1.71.0 REQUIRED COMPONENTS system)

if(NOT EXISTS ${BOTAN_ROOT_DIR})
    message(FATAL_ERROR "Please provide BOTAN_ROOT_DIR, pointing to the "
        "directory that contains the botan library and include directory.")
endif ()

set(BOTAN_INCLUDE_DIR "${BOTAN_ROOT_DIR}/include")
include_directories(${Boost_INCLUDE_DIRS} ${BOTAN_INCLUDE_DIR})

find_library(BOTAN_LIBRARY botan PATHS ${BOTAN_ROOT_DIR})

if(UNIX AND NOT APPLE)
    # link pthread on linux
    set (PTHREAD pthread)
endif ()

add_executable(${PROJECT_NAME} server.cpp)
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${BOTAN_LIBRARY} ${PTHREAD})
