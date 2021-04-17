
cmake_minimum_required(VERSION 3.14 FATAL_ERROR)
project(libRFC 
    LANGUAGES C CXX
    VERSION 0.0.0)

# Setting global compiler flags, options, release options
# are in this file.
INCLUDE( cmake/Options.cmake )

# =======
# Library 
# =======

add_library(libRFC
    libRFC/libRFC.cpp
    )

target_include_directories(libRFC PUBLIC include)
target_include_directories(libRFC PRIVATE libRFC)


# ============
# Installation
# ============

# By default we will By default, we prefer Release configuration for our project.
# The user will be able to set this with the CMAKE_BUILD_TYPE variable and we
# check whether that is the case. If not, we set it ourselves to the default,
# sensible value:
message(STATUS "Project will be installed to ${CMAKE_INSTALL_PREFIX}")
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()
message(STATUS "Build type set to ${CMAKE_BUILD_TYPE}")

# Library installation.  Provide the ability for package maintainers to
# overrride installation directories.
include(GNUInstallDirs)
install(TARGETS libRFC
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        COMPONENT       libRFC_RunTime
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        COMPONENT           libRFC_RunTime
        NAMELINK_COMPONENT  libRFC_Development
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        COMPONENT   libRFC_Development
)

# =========
# Packaging 
# =========

include(cmake/CMakeCPack.cmake)

# =======
# Testing
# =======
enable_testing()
find_package(GTest REQUIRED)
if(NOT GTEST_LIBRARY)
    message("GTest not found!")
endif()

add_executable(libRFCTest
  test/test_main.cpp
)
target_link_libraries(libRFCTest PUBLIC libRFC ${GTEST_BOTH_LIBRARIES} )
target_include_directories(libRFCTest PUBLIC include)
target_include_directories(libRFCTest PRIVATE ${GTEST_INCLUDE_DIRS} libRFC)

add_test(libRFCTest libRFCTest)

# =======
# Doxygen
# =======
include(doxygen)