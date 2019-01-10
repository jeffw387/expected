cmake_minimum_required(VERSION 3.0)

project(expected)

option(EXPECTED_ENABLE_TESTS "Enable tests." ON)
option(EXPECTED_ENABLE_DOCS "Enable documentation." ON)

add_library(tl_expected INTERFACE)
target_sources(tl_expected INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/tl/expected.hpp)
target_include_directories(tl_expected INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})

# Prepare "tl_Catch" library for other executables
set(CATCH_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/test)
add_library(tl_Catch INTERFACE)
target_include_directories(tl_Catch INTERFACE ${CATCH_INCLUDE_DIR})

if(EXPECTED_ENABLE_TESTS)
  # Make test executable
  set(TEST_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/tests/main.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/tests/extensions.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/tests/assignment.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/tests/emplace.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/tests/issues.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/tests/bases.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/tests/observers.cpp
                   ${CMAKE_CURRENT_SOURCE_DIR}/tests/constructors.cpp)

  add_executable(tl_expected_tests ${TEST_SOURCES})

  target_link_libraries(tl_expected_tests tl_Catch tl_expected)

  set(CXXSTD 14 CACHE STRING "C++ standard to use, default C++14")
  set_property(TARGET tl_expected_tests PROPERTY CXX_STANDARD ${CXXSTD})
  set_property(TARGET tl_expected_tests PROPERTY CXX_FLAGS "-Wall -Wextra")
endif()

if(EXPECTED_ENABLE_DOCS)
  find_package(standardese) # find standardese after installation

  # generates a custom target that will run standardese to generate the documentation
  if(standardese_FOUND)
    standardese_generate(tl_expected
                         INCLUDE_DIRECTORY tl
                         CONFIG ${CMAKE_CURRENT_SOURCE_DIR}/standardese.config
                         INPUT tl/expected.hpp)
  endif()
endif()
