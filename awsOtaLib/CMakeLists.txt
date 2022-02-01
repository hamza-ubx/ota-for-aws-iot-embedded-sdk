cmake_minimum_required ( VERSION 3.13.0 )
project ( "OTA unit test"
          VERSION 1.0.0
          LANGUAGES C )

# Allow the project to be organized into folders.
set_property( GLOBAL PROPERTY USE_FOLDERS ON )

# Use C90 if not specified.
if( NOT DEFINED CMAKE_C_STANDARD )
    set( CMAKE_C_STANDARD 90 )
endif()
if( NOT DEFINED CMAKE_C_STANDARD_REQUIRED )
    set( CMAKE_C_STANDARD_REQUIRED ON )
endif()

# Do not allow in-source build.
if( ${PROJECT_SOURCE_DIR} STREQUAL ${PROJECT_BINARY_DIR} )
    message( FATAL_ERROR "In-source build is not allowed. Please build in a separate directory, such as ${PROJECT_SOURCE_DIR}/build." )
endif()

# Set global path variables.
get_filename_component(__MODULE_ROOT_DIR "${CMAKE_CURRENT_LIST_DIR}/.." ABSOLUTE)
set(MODULE_ROOT_DIR ${__MODULE_ROOT_DIR} CACHE INTERNAL "OTA repository root.")

# Configure options to always show in CMake GUI.
option( BUILD_CLONE_SUBMODULES
        "Set this to ON to automatically clone any required Git submodules. When OFF, submodules must be manually cloned."
        ON )

# Set output directories.
set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin )
set( CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib )
set( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib )

# =============================================

# Include filepaths for source and include.
include( ${MODULE_ROOT_DIR}/otaFilePaths.cmake )

# Target for Coverity analysis that builds the library.
add_library( awsOtaLib
    ${OTA_SOURCES}
    ${OTA_MQTT_SOURCES}
    ${OTA_HTTP_SOURCES} )

# Build OTA library target without custom config dependency
target_compile_definitions( awsOtaLib PUBLIC OTA_DO_NOT_USE_CUSTOM_CONFIG=1 )

# OTA include path.
target_include_directories( awsOtaLib PUBLIC
    ${OTA_INCLUDE_PUBLIC_DIRS}  )
target_include_directories( awsOtaLib PRIVATE
    ${OTA_INCLUDE_PRIVATE_DIRS} )
