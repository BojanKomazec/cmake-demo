cmake_minimum_required(VERSION 3.10)

project(
   CMakeDemo
   VERSION 1.0.0
   DESCRIPTION "CMake Demo"
)

message("Current project source dir is ${PROJECT_SOURCE_DIR}")
message("Current binary dir is ${CMAKE_CURRENT_BINARY_DIR}")

if(${PROJECT_SOURCE_DIR} STREQUAL ${PROJECT_BINARY_DIR})
    message(FATAL_ERROR "In-source builds not allowed. Please make a new directory (called a build directory) and run CMake from there.")
endif()

message(STATUS "BRAND=${BRAND}")

include(${CMAKE_SOURCE_DIR}/brands/cmake-${BRAND}-params.cmake)

message(STATUS "COMPANY_NAME=${COMPANY_NAME}")

configure_file(Output.txt.in Output.txt)

# To be executed on Linux with PowerShell installed
# TODO: add execution condition check (check if powershell is installed on linux)
execute_process(
   COMMAND pwsh "-Command" ". ./echo.ps1; Show-Argument \
-Arg0 ${COMPANY_NAME}"

   WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}

   OUTPUT_VARIABLE CMD_STDOUT
   ERROR_VARIABLE CMD_ERROR
   RESULT_VARIABLE CMD_RESULT
)

MESSAGE(STATUS "Command STDOUT: ${CMD_STDOUT}")
MESSAGE(STATUS "Command ERROR:" ${CMD_ERROR})
MESSAGE(STATUS "Command RESULT:" ${CMD_RESULT})