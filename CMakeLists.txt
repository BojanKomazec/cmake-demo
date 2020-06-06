cmake_minimum_required(VERSION 3.10)

project(
   CMakeDemo
   VERSION 1.0.0
   DESCRIPTION "CMake Demo"
)

message(STATUS "BRAND=${BRAND}")

include(${CMAKE_SOURCE_DIR}/brands/cmake-${BRAND}-params.cmake)

message(STATUS "COMPANY_NAME=${COMPANY_NAME}")

configure_file(Output.txt.in Output.txt)