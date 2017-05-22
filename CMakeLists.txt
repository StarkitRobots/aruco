# ----------------------------------------------------------------------------
#   Basic Configuration
# ----------------------------------------------------------------------------
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

PROJECT(aruco)
set(PROJECT_VERSION "2.0.19")
string(REGEX MATCHALL "[0-9]" PROJECT_VERSION_PARTS "${PROJECT_VERSION}")
list(GET PROJECT_VERSION_PARTS 0 PROJECT_VERSION_MAJOR)
list(GET PROJECT_VERSION_PARTS 1 PROJECT_VERSION_MINOR)
list(GET PROJECT_VERSION_PARTS 2 PROJECT_VERSION_PATCH)
set(PROJECT_SOVERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}")

#------------------------------------------------------
# Lib Names and Dirs
#------------------------------------------------------

OPTION(USE_DOUBLE_PRECISION_PNP "Set Double/float precision for posetracker" ON)
OPTION(BUILD_UTILS	"Set to OFF to not compile utils " ON)
OPTION(BUILD_TESTS	"Set to OFF to not compile utils " OFF)
OPTION(BUILD_SHARED_LIBS 	"Set to OFF to build static libraries" ON)
OPTION(BUILD_GLSAMPLES 	"Set to OFF to build static libraries" OFF)

# ----------------------------------------------------------------------------
#   Find Dependencies
# ----------------------------------------------------------------------------

find_package(catkin REQUIRED COMPONENTS urdfdom_headers console_bridge)

FIND_PACKAGE(OpenCV REQUIRED)
set (ARUCO_REQUIRED_LIBRARIES ${OpenCV_LIBS})
INCLUDE_DIRECTORIES(${OpenCV_INCLUDE_DIRS})


catkin_package(
  INCLUDE_DIRS src
  LIBRARIES aruco
  CATKIN_DEPENDS Eigen
)

set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

#Enabling OpenMP
find_package(OpenMP  )
if (OPENMP_FOUND)
  set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  add_definitions(-DUSE_OMP)
endif()


## ----------------------------------------------------------------------------
##   Program Optimization and debug (Extracted from OpenCV)
## ----------------------------------------------------------------------------
#set(WARNINGS_ARE_ERRORS 		OFF CACHE BOOL "Treat warnings as errors")
#set(WHOLE_PROGRAM_OPTIMIZATION 	OFF CACHE BOOL "Flags for whole program optimization.")
#
#set(EXTRA_C_FLAGS "")
#set(EXTRA_C_FLAGS_RELEASE "")
#set(EXTRA_C_FLAGS_DEBUG "")
#set(EXTRA_EXE_LINKER_FLAGS "")
#set(EXTRA_EXE_LINKER_FLAGS_RELEASE "")
#set(EXTRA_EXE_LINKER_FLAGS_DEBUG "")
#IF(CMAKE_COMPILER_IS_GNUCXX OR MINGW OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
#    set(ENABLE_PROFILING 		OFF CACHE BOOL "Enable profiling in the GCC compiler (Add flags: -g -pg)")
#    set(USE_OMIT_FRAME_POINTER 	ON CACHE BOOL "Enable -fomit-frame-pointer for GCC")
#    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES arm*) # We can use only -O2 because the -O3 causes gcc crash
#        set(USE_O2 ON CACHE BOOL "Enable -O2 for GCC")
#        set(USE_FAST_MATH ON CACHE BOOL "Enable -ffast-math for GCC")
#    endif()
#    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES powerpc*)
#        set(USE_O3 ON CACHE BOOL "Enable -O3 for GCC")
#        set(USE_POWERPC ON CACHE BOOL "Enable PowerPC for GCC")
#    endif ()
#    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES amd64* OR ${CMAKE_SYSTEM_PROCESSOR} MATCHES x86_64*)
#        set(USE_O3 ON CACHE BOOL "Enable -O3 for GCC")
#        set(USE_FAST_MATH ON CACHE BOOL "Enable -ffast-math for GCC")
#        set(USE_MMX ON CACHE BOOL "Enable MMX for GCC")
#        set(USE_SSE ON CACHE BOOL "Enable SSE for GCC")
#        set(USE_SSE2 ON CACHE BOOL "Enable SSE2 for GCC")
#        set(USE_SSE3 ON CACHE BOOL "Enable SSE3 for GCC")
#    endif()
#    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES i686* OR ${CMAKE_SYSTEM_PROCESSOR} MATCHES x86)
#        set(USE_O3 ON CACHE BOOL "Enable -O3 for GCC")
#        set(USE_FAST_MATH ON CACHE BOOL "Enable -ffast-math for GCC")
#        set(USE_MMX ON CACHE BOOL "Enable MMX for GCC")
#        set(USE_SSE OFF CACHE BOOL "Enable SSE for GCC")
#        set(USE_SSE2 OFF CACHE BOOL "Enable SSE2 for GCC")
#        set(USE_SSE3 OFF CACHE BOOL "Enable SSE3 for GCC")
#    endif ()
#
#    set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -Wall")
#
#    if(WARNINGS_ARE_ERRORS)
#        set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -Werror")
#    endif()
#
#    # The -Wno-long-long is required in 64bit systems when including sytem headers.
#    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES x86_64* OR ${CMAKE_SYSTEM_PROCESSOR} MATCHES amd64*)
#		set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -Wno-long-long")
#    endif()
#
#    # Whole program optimization
#    if(WHOLE_PROGRAM_OPTIMIZATION)
#        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -fwhole-program --combine")
#    endif()
#
#    # Other optimizations
#    if(USE_OMIT_FRAME_POINTER)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -fomit-frame-pointer")
#    endif()
#    if(USE_O2)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -O2")
#    endif()
#    if(USE_O3)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -O3")
#    endif()
#    if(USE_FAST_MATH)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -ffast-math")
#    endif()
#    if(USE_POWERPC)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -mcpu=G3 -mtune=G5")
#    endif()
#    if(USE_MMX)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -mmmx")
#    endif()
#    if(USE_SSE)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -msse")
#    endif()
#    if(USE_SSE2)
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -msse2")
#    endif()
#    if(USE_SSE3 AND NOT MINGW) # SSE3 should be disabled under MingW because it generates compiler errors
#       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -msse3")
#    endif()
#
#    if(ENABLE_PROFILING)
#        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -pg -g")
#    else()
#        if(NOT APPLE)
#            set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -ffunction-sections")
#        endif()
#    endif()
#
#    # Parallel mode
#    if(ENABLE_OPENMP)
#        set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS}  -fopenmp")
#        set(LINKER_LIBS ${LINKER_LIBS} gomp)
#    endif()
#    IF(${CMAKE_SYSTEM_PROCESSOR} MATCHES armv7l) # In ARM_COrtex8 with neon, enalble vectorized operations
#	SET(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -mcpu=cortex-a8 -mfpu=neon -mfloat-abi=softfp -ftree-vectorize ")
#    ENDIF()
#
#
#    set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -DNDEBUG")
#    set(EXTRA_C_FLAGS_DEBUG "-g3 -O0 -DDEBUG -D_DEBUG -W -Wextra -Wno-return-type")
#
#    MESSAGE( STATUS "-------------------------------------------------------------------------------" )
#    message( STATUS "GNU COMPILER")
#    MESSAGE( STATUS "-------------------------------------------------------------------------------" )
#
#    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${EXTRA_C_FLAGS} -std=c++11")
#    SET(CMAKE_CXX_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE}")
#    SET(CMAKE_CXX_FLAGS_DEBUG "${EXTRA_C_FLAGS_DEBUG}")
#    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${EXTRA_EXE_LINKER_FLAGS}")
#    SET(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} ${EXTRA_EXE_LINKER_FLAGS_RELEASE}")
#    SET(CMAKE_EXE_LINKER_FLAGS_DEBUG "${CMAKE_EXE_LINKER_FLAGS_DEBUG} ${EXTRA_EXE_LINKER_FLAGS_DEBUG}")
#ENDIF	()


#------------------------------------------------
# DIRS
#------------------------------------------------
ADD_SUBDIRECTORY(src)
IF(BUILD_UTILS)
  ADD_SUBDIRECTORY(utils)
  ADD_SUBDIRECTORY(utils_markermap)
  ADD_SUBDIRECTORY(utils_calibration)
ENDIF()
IF(BUILD_TESTS)
  ADD_SUBDIRECTORY(tests)
ENDIF()