cmake_minimum_required(VERSION 3.0)
project(dbd)

Include(FetchContent)

set(DBD_BUILD_TESTS OFF CACHE BOOL "If dbd should build the automatic test suite")

add_library(dbd
  src/dbd_lexer.cpp include/dbd_lexer.hpp
  src/dbd_parser.cpp include/dbd_parser.hpp
)
set_property(TARGET dbd PROPERTY CXX_STANDARD 20)
target_include_directories(dbd PUBLIC include)

if (${DBD_BUILD_TESTS})
  set(DBD_CATCH2_TARGET "Catch2" CACHE STRING "catch2 library target to use for dbd")
  if (TARGET ${DBD_CATCH2_TARGET})
  else()
    FetchContent_Declare(
      Catch2
      GIT_REPOSITORY https://github.com/catchorg/Catch2.git
      GIT_TAG        v3.1.0
    )
    FetchContent_MakeAvailable(Catch2)
    set(DBD_CATCH2_TARGET "Catch2" CACHE STRING "catch2 library target to use for dbd" FORCE)
  endif()
  add_executable(dbd_test
    test/dbd_lexer_tests.cpp
    test/dbd_parser_tests.cpp
  )
  target_link_libraries(dbd_test PRIVATE dbd Catch2::Catch2WithMain)
  set_property(TARGET dbd_test PROPERTY CXX_STANDARD 20)
  target_compile_definitions(dbd_test PRIVATE DBD_DEFINITIONS_PATH="${CMAKE_CURRENT_SOURCE_DIR}/../../definitions")
  target_compile_definitions(dbd_test PUBLIC "$<$<CONFIG:DEBUG>:DBD_DEBUG>")
endif()