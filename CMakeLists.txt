# This is a blank project to use as a template to build C projects

cmake_minimum_required(VERSION 3.24)
project(blank_project VERSION 0.1)

# Debug mode
set(DO_OPT 0)
set(DO_DANGER 0)

# Paths
file(GLOB SOURCES "src/*.c")
set(HEADERS "include")

# Base Flags
set(DEBUG_FLAGS "-Wall -Wextra -pedantic -Wfloat-equal -Wundef -Wshadow \
    -Wpointer-arith -Wwrite-strings -Waggregate-return -Wunreachable-code \
    -Wstrict-prototypes -Wstrict-overflow=2 -Wcast-align -Wcast-qual \
     -Wconversion -Wswitch-default -Wswitch-enum")
set(OPTIMIZATION_FLAGS "-O3 -march=native -funroll-loops -finline-functions ")
set(DANGER_FLAGS "-O3 -march=native  -funroll-loops -finline-functions  -Ofast -ffast-math")
set(LIB_FLAGS "${LIB_FLAGS}") # -fopenmp

# Import Data files
file(COPY "dat" DESTINATION .)

# Import Library sources
file(GLOB mock_lib_a_SRC "deps/mock_lib_a/src/*.c")

# Include Library headers
set(mock_lib_a_HEADERS "deps/mock_lib_a/include")
set(mock_lib_b_HEADERS "deps/mock_lib_b") # header only lib

# Find needed packages that should be installed on the compiler of the system.
# For example:
# # OpenMP
# find_package(OpenMP 4.5 REQUIRED)

# if (OpenMP_FOUND)
# message("OpenMP v4.5 found!")
# else ()
# message(FATAL_ERROR "OpenMP v4.5 not found")
# endif ()

# Make static libraries
if(DEFINED mock_lib_a_SRC AND DEFINED mock_lib_a_HEADERS)
    add_library(mock_lib_a STATIC ${mock_lib_a_SRC})
    target_compile_options(mock_lib_a PRIVATE -w)
    target_include_directories(mock_lib_a PRIVATE ${mock_lib_a_HEADERS})

    include_directories(${mock_lib_a_HEADERS})
    set(STATIC_LIBS ${STATIC_LIBS} mock_lib_a)
    message(". . . mock static library created with success!")
endif()

# Include header-only libraries
if(DEFINED mock_lib_b_HEADERS)
    message(". . . mock header library included!")
    include_directories(${mock_lib_b_HEADERS})
endif()

# Set flags according to the mode of compilation
if(DO_OPT EQUAL 0)
    message("Debug flags set!")
    set(CMAKE_C_FLAGS_DEBUG "${DEBUG_FLAGS} ${LIB_FLAGS}"
        CACHE STRING "C Flags" FORCE)
else()
    if(DO_DANGER EQUAL 0)
        message("Optimization flags set!")
        set(CMAKE_C_FLAGS "${OPTIMIZATION_FLAGS} ${LIB_FLAGS}"
            CACHE STRING "C Flags" FORCE)
    else()
        message("Dangerous optimization flags set!")
        set(CMAKE_C_FLAGS "${OPTIMIZATION_FLAGS} ${DANGER_FLAGS}} ${LIB_FLAGS}"
            CACHE STRING "C Flags" FORCE)
    endif()
endif()

# Gather game sources
add_executable(${PROJECT_NAME} ${SOURCES})
include_directories(${HEADERS})

# Linking to Pluto
target_link_libraries(${PROJECT_NAME} ${STATIC_LIBS})

set_target_properties(${PROJECT_NAME}
    PROPERTIES
    C_STANDARD 99
    C_STANDARD_REQUIRED YES
    C_EXTENSIONS NO
)