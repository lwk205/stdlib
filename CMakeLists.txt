cmake_minimum_required(VERSION 3.14.0)
project(stdlib Fortran)
enable_testing()

include(${CMAKE_SOURCE_DIR}/cmake/stdlib.cmake)

# --- compiler options
if(CMAKE_Fortran_COMPILER_ID STREQUAL GNU)
  add_compile_options(-fimplicit-none)
elseif(CMAKE_Fortran_COMPILER_ID STREQUAL Intel)
  add_compile_options(-warn declarations)
elseif(CMAKE_Fortran_COMPILER_ID STREQUAL PGI)
  add_compile_options(-Mdclchk)
endif()

# --- compiler feature checks
include(CheckFortranSourceCompiles)
include(CheckFortranSourceRuns)
check_fortran_source_compiles("error stop i; end" f18errorstop SRC_EXT f90)
check_fortran_source_compiles("real, allocatable :: array(:, :, :, :, :, :, :, :, :, :); end" f03rank SRC_EXT f90)
check_fortran_source_runs("use, intrinsic :: iso_fortran_env, only : real128; real(real128) :: x; x = x+1; end" f03real128)

if(DEFINED CMAKE_MAXIMUM_RANK)
  set(CMAKE_MAXIMUM_RANK ${CMAKE_MAXIMUM_RANK})
endif()

# --- find preprocessor
find_program(FYPP fypp)
if(NOT FYPP)
  message(FATAL_ERROR "Preprocessor fypp not found!")
endif()

add_subdirectory(src)
