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

# To be executed on Linux with PowerShell installed
# TODO: add execution condition check (check if powershell is installed on linux)
execute_process(
   COMMAND pwsh "-Command" ". ./echo.ps1; Show-Argument \
-Arg0 ${COMPANY_NAME}"

   OUTPUT_VARIABLE CMD_STDOUT
   ERROR_VARIABLE CMD_ERROR
   RESULT_VARIABLE CMD_RESULT
)

MESSAGE(STATUS "Command STDOUT: ${CMD_STDOUT}")
MESSAGE(STATUS "Command ERROR:" ${CMD_ERROR})
MESSAGE(STATUS "Command RESULT:" ${CMD_RESULT})