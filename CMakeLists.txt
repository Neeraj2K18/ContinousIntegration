cmake_minimum_required(VERSION 3.22)
# CMAKE_VERSION <major>.<minor>.<date>(YYMMDD) or <patch>
project(
  ContinousIntegration
  VERSION 0.0.220213
  LANGUAGES CXX)
message("-- PROJECT = ${CMAKE_PROJECT_NAME} v${CMAKE_PROJECT_VERSION}")

# Set Options/FLAGS
option(NAT_PCKG "Display packages which are natively used for the project." OFF)
option(DEP_PCKG "Display packages dependent packages used for the project." OFF)

# Set the compiler options
set(CMAKE_CXX_STANDARD 20) # > Set cmake Cxx standard to 11/14/17/20
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Include modular cmake files include(scripts/cmake/catch2Test.cmake)
# include(scripts/cmake/updateSubmodule.cmake)

# For Catch2 Test include(CTest) enable_testing() add_subdirectory(test)

if(NAT_PCKG)
  include(scripts/cmake/nativePckg.cmake)
endif()

if(DEP_PCKG)
  include(scripts/cmake/depPckg.cmake)
endif()

add_subdirectory(src)
add_subdirectory(lib)

if(MSVC)
  # warning level 4 and all warnings as errors
  add_compile_options(/W4 /WX)
else()
  # lots of warnings and all warnings as errors
  add_compile_options(-Wall -Wextra -pedantic -Werror)
endif()

# create main executable file
add_executable(${PROJECT_NAME} src lib)

# Used to correct the include header name
target_include_directories(${PROJECT_NAME} PUBLIC lib)

target_link_directories(${PROJECT_NAME} PRIVATE lib/)

target_link_libraries(
  ${PROJECT_NAME}
  PRIVATE src
  PRIVATE lib)

# For making installation files for the Project run "$cpack" in build directory
set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)
