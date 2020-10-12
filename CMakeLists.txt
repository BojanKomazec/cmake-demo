cmake_minimum_required(VERSION 3.10)

project(
   CMakeDemo
   VERSION 1.0.0
   DESCRIPTION "CMake Demo"
)

include (${CMAKE_CURRENT_SOURCE_DIR}/util/util_demo.cmake)

message("PROJECT_SOURCE_DIR = ${PROJECT_SOURCE_DIR}")
message("CMAKE_CURRENT_BINARY_DIR = ${CMAKE_CURRENT_BINARY_DIR}")

if (NOT EMPTY_STRING_VAR)
   # this is executed if EMPTY_STRING_VAR is not defined
   message("EMPTY_STRING_VAR is not defined")
endif()

set(EMPTY_STRING_VAR "")
if (NOT EMPTY_STRING_VAR)
    # this is executed if EMPTY_STRING_VAR is defined but has value which is empty string
   message("EMPTY_STRING_VAR is defined and is an empty string")
endif()

if(${PROJECT_SOURCE_DIR} STREQUAL ${PROJECT_BINARY_DIR})
    message(FATAL_ERROR "In-source builds not allowed. Please make a new directory (called a build directory) and run CMake from there.")
endif()

message(STATUS "BRAND=${BRAND}")
set(BRANDS branda brandb brandc)
if(NOT ${BRAND} IN_LIST BRANDS)
   # message(FATAL_ERROR ...) will cause cmake to exit
   message(FATAL_ERROR "BRAND must have one of these values: ${BRANDS}")
endif()

# variables set via command line can be overwritten
if (BRAND)
   message(STATUS "BRAND (original value) = ${BRAND}")
   set(BRAND_OLD ${BRAND})

   # WARNING: THIS IS WRONG: set(${BRAND} "dummy_brand")
   set(BRAND "dummy_brand")

   message(STATUS "BRAND (after overwriting) = ${BRAND}")
   set(BRAND ${BRAND_OLD})
endif()

# This condition is false if:
# - OUTPUT_DIR is omitted e.g.: cmake -DBRAND=branda ..
# - OUTPUT_DIR value is omitted e.g.: cmake -DBRAND=branda -DOUTPUT_DIR= ..
# - OUTPUT_DIR value is empty string e.g.: cmake -DBRAND=branda -DOUTPUT_DIR="" ..
if (OUTPUT_DIR)
   message("Variable OUTPUT_DIR is defined.")
endif()

if (${OUTPUT_DIR})
   message("${OUTPUT_DIR} is defined.")
endif()

# This condition is true if:
# - OUTPUT_DIR is omitted e.g.: cmake -DBRAND=branda ..
# - OUTPUT_DIR value is omitted e.g.: cmake -DBRAND=branda -DOUTPUT_DIR= ..
# - OUTPUT_DIR value is empty string e.g.: cmake -DBRAND=branda -DOUTPUT_DIR="" ..
if (NOT OUTPUT_DIR)
   message("Variable OUTPUT_DIR is NOT defined.")
endif()

# This condition is false if:
# - OUTPUT_DIR is omitted e.g.: cmake -DBRAND=branda ..
# - OUTPUT_DIR value is omitted e.g.: cmake -DBRAND=branda -DOUTPUT_DIR= ..
# - OUTPUT_DIR value is empty string e.g.: cmake -DBRAND=branda -DOUTPUT_DIR="" ..
# IMPORTANT: If OUTPUT_DIR is NOT defined this condition will be false!
if (NOT ${OUTPUT_DIR})
   message("\"${OUTPUT_DIR}\" is NOT defined.")
endif()

if (OUTPUT_DIR)
   if(IS_ABSOLUTE ${OUTPUT_DIR})
      message("${OUTPUT_DIR} is absolute path")
   else()
      message("${OUTPUT_DIR} is relative path")
   endif()
endif()

if(NOT OUTPUT_DIR)
   message("Setting OUTPUT_DIR to CMAKE_CURRENT_BINARY_DIR"...)
   set(OUTPUT_DIR "${CMAKE_CURRENT_BINARY_DIR}")
   message("OUTPUT_DIR = ${OUTPUT_DIR}")
endif()

if (OUTPUT_DIR)
   message("OUTPUT_DIR (before overwriting) = ${OUTPUT_DIR}")
   set(OUTPUT_DIR "dummy")
   message("OUTPUT_DIR (after overwriting) = ${OUTPUT_DIR}")
endif()

# If variable OUTPUT_DIR is not defined the following command issues error:
#  get_filename_component called with incorrect number of arguments
get_filename_component(OUTPUT_DIR ${OUTPUT_DIR} ABSOLUTE)

message("OUTPUT_DIR = ${OUTPUT_DIR}")
file(MAKE_DIRECTORY ${OUTPUT_DIR})

include(${CMAKE_SOURCE_DIR}/brands/cmake-${BRAND}-params.cmake)

message(STATUS "COMPANY_NAME=${COMPANY_NAME}")

configure_file(Output.txt.in Output.txt)

#
# execute_process()
#

execute_process(
   COMMAND echo "execute_process() is executed in configuration stage"
)

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
message(STATUS "Command STDOUT: ${CMD_STDOUT}")
message(STATUS "Command ERROR:" ${CMD_ERROR})
message(STATUS "Command RESULT:" ${CMD_RESULT})

# This example shows how to check if OS is Windows and also
# how to persist the output of the process in the variable.
IF (WIN32)
   execute_process(
      COMMAND dir
      RESULT_VARIABLE ls_result
   )
ELSE()
   execute_process(
      COMMAND ls
      RESULT_VARIABLE ls_result
   )
ENDIF()
message("ls_result='${ls_result}'")

#
# Demo how to call a function from included .cmake file
#
foo()

#
# As ALL is specified this target will be "built" when we execute either of these commands:
#     cmake --build .
#     cmake --build . --target all
#     cmake --build . --target print_cmake_version
#
add_custom_target(print_cmake_version ALL
   COMMAND cmake --version
   COMMENT "Printing CMake version"
   VERBATIM
   USES_TERMINAL
)

#
# As ALL is NOT specified this target will be "built" when we execute:
#     cmake --build . --target print_timestamp
#
add_custom_target(print_timestamp
   COMMAND date
   COMMENT "Printing current time"
   VERBATIM
   USES_TERMINAL
)

set(TMP_FILE "deleteme.tmp")

add_custom_command(
   OUTPUT ${TMP_FILE}
   COMMAND touch ${TMP_FILE}
   COMMENT "Creating temp file."
   VERBATIM
)

# Because this target depends on TMP_FILE, a command which creates it will be executed.
# To build this target run either of these commands:
#     cmake --build .
#     cmake --build . --target all
#     cmake --build . --target create_tmp_file
#
add_custom_target(create_tmp_file
   ALL
   DEPENDS ${TMP_FILE}
)