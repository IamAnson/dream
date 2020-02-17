cmake_minimum_required(VERSION 3.10)
project(dream)

set(JSON_ROOT ${PROJECT_SOURCE_DIR}/3rd/jsoncpp-1.8.4)
set(LINKER_LIBS "")

include_directories(${PROJECT_SOURCE_DIR}/include)
include_directories(${PROJECT_SOURCE_DIR}/src)
include_directories(${JSON_ROOT}/include)

find_library(JSON_LIBRARY jsoncpp
        PATHS ${JSON_ROOT}
        PATH_SUFFIXES build/src/lib_json/Debug build/src/lib_json)
list(APPEND LINKER_LIBS ${JSON_LIBRARY})

add_definitions(-D_DREAM)

file(GLOB_RECURSE HEADERS ${PROJECT_SOURCE_DIR}/src/*.h)
source_group("Header Files" FILES ${HEADERS})
aux_source_directory(${PROJECT_SOURCE_DIR}/src DIR_DREAM_SRCS)
add_library(dream STATIC ${DIR_DREAM_SRCS} ${HEADERS})
target_link_libraries(dream ${LINKER_LIBS})

# config with test
set(GTEST_ROOT ${PROJECT_SOURCE_DIR}/3rd/googletest-1.8.0)
include(GoogleTest)

set(GTEST_DIR ${GTEST_ROOT}/googletest)
include_directories(${GTEST_DIR}/include)
include_directories(${GTEST_DIR})
add_definitions(-D_SILENCE_TR1_NAMESPACE_DEPRECATION_WARNING)

set(GMOCK_DIR ${GTEST_ROOT}/googlemock)
include_directories(${GMOCK_DIR}/include)
include_directories(${GMOCK_DIR})

aux_source_directory(${PROJECT_SOURCE_DIR}/test TEST_CASES)
add_executable(dream-test ${TEST_CASES}
${GTEST_DIR}/src/gtest-all.cc
${GMOCK_DIR}/src/gmock-all.cc
${GMOCK_DIR}/src/gmock_main.cc)
target_link_libraries(dream-test dream)

if (NOT WIN32)
    include_directories("usr/local/include")
    include_directories("usr/local/lib")
    target_link_libraries(dream-test pthread)
endif()

enable_testing()
add_test(NAME dream-test COMMAND dream-test)
gtest_add_tests(TARGET dream-test)
