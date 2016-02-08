project(FLIF)
cmake_minimum_required(VERSION 3.1)

#
# FLIF version number
set(FLIF_VERSION_MAJOR 0)
set(FLIF_VERSION_MINOR 1)
set(FLIF_VERSION_PATCH 1)

#
# Option to select whether we build shared or static libraries
option(FLIF_BUILD_SHARED_LIBS "Build shared libraries?" OFF)
if(FLIF_BUILD_SHARED_LIBS)
    set(FLIF_LIB_TYPE SHARED)
else(FLIF_BUILD_SHARED_LIBS)
    set(FLIF_LIB_TYPE STATIC)
endif(FLIF_BUILD_SHARED_LIBS)


#
# Locate png
find_package(PNG REQUIRED)
add_definitions(${PNG_DEFINITIONS})
include_directories(${PNG_INCLUDE_DIRS})


#
# Build all the things...
add_subdirectory(src)
add_subdirectory(tools)
add_subdirectory(doc)


#
# Optionally include CPack to generate source/binary packages
option(FLIF_USE_CPACK "Include CPack for package generation?" ON)
if(FLIF_USE_CPACK)
    set(CPACK_PACKAGE_VERSION_MAJOR ${FLIF_VERSION_MAJOR})
    set(CPACK_PACKAGE_VERSION_MINOR ${FLIF_VERSION_MINOR})
    set(CPACK_PACKAGE_VERSION_PATCH ${FLIF_VERSION_PATCH})
    set(CPACK_PACKAGE_DESCRIPTION "Free Lossless Image Format")
    include(CPack)
endif(FLIF_USE_CPACK)
