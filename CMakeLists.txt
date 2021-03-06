project(Corexapod)
cmake_minimum_required(VERSION 2.8.2)

if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
  message(FATAL_ERROR "In-source builds not allowed. Please make a new directory (called a build directory) and run CMake from there. You may need to remove CMakeCache.txt. ")
endif()

if (NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Debug")
endif()

string(TOLOWER "${CMAKE_BUILD_TYPE}" cmake_build_type_tolower)
if(    NOT cmake_build_type_tolower STREQUAL "debug"
   AND NOT cmake_build_type_tolower STREQUAL "release"
   AND NOT cmake_build_type_tolower STREQUAL "relwithdebinfo")
  message(FATAL_ERROR "Unknown build type \"${CMAKE_BUILD_TYPE}\". Allowed values are Debug, Release, RelWithDebInfo (case-insensitive).")
endif()

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/Modules/" ${CMAKE_MODULE_PATH})

# set include dirs
include_directories(${PROJECT_SOURCE_DIR}/include)

# find necessary packages
set( Boost_USE_STATIC_LIBS ON ) 
find_package(Boost 1.40 COMPONENTS thread date_time program_options filesystem system regex REQUIRED)
include_directories(${BOOST_INCLUDE_DIR})

find_package(Eigen3 REQUIRED)
include_directories(${EIGEN3_INCLUDE_DIR})

# set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
# set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

set( LIB_SRC
	src/engine.cpp
	src/serial.cpp
	src/uscwrapper.cpp
)

add_library( foundation SHARED ${LIB_SRC} )
target_link_libraries( foundation ${BOOST_LIBRARIES} )

add_library( uscengine SHARED src/uscengine.c )

add_executable( demo src/app/uscwrapperdemo.c )
target_link_libraries( demo uscengine )

add_executable( demo2 src/app/uscdemo.cpp )
target_link_libraries( demo2 foundation )
