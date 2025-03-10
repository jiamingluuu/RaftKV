cmake_minimum_required(VERSION 3.5)
project(raft-kv)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_STANDARD 17)

add_definitions(-Wall)
add_definitions(-DDEBUG)

include_directories(${CMAKE_SOURCE_DIR})

find_package(PkgConfig REQUIRED)
pkg_check_modules(dependencies
                  glib-2.0>=2.10 REQUIRED
                  msgpack-c REQUIRED
                  hiredis REQUIRED
                  rocksdb REQUIRED)
include_directories(${dependencies_INCLUDE_DIRS})
set(LIBS
    ${dependencies_LIBRARIES})

find_package(Boost REQUIRED COMPONENTS system filesystem)
include_directories(${Boost_INCLUDE_DIRS} ${THIRD_PARTY_INCLUDE_DIR})
set(LIBS
    ${LIBS}
    pthread
    Boost::system
    Boost::filesystem
    )

add_subdirectory(raft-kv)

pkg_check_modules(GoogleTest gtest_main)
if (GoogleTest_FOUND)
    include_directories(${GoogleTest_INCLUDE_DIRS})
    set(LIBS
        ${LIBS}
        ${GoogleTest_LIBRARIES})
    add_subdirectory(tests)
    enable_testing()
endif (GoogleTest_FOUND)

configure_file(Procfile.in Procfile)
