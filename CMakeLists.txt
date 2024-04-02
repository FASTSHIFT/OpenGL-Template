cmake_minimum_required(VERSION 3.10)
project(Simulator)

# C/C++ version
set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 14)

# Platform config
## Common
option(ENABLE_DEBUG "Enable debug build" ON)

if(ENABLE_DEBUG)
  message(STATUS "Build type: Debug")
  add_definitions(-DDEBUG)
  add_definitions(-DLV_USE_ASSERT=1)
  set(CMAKE_BUILD_TYPE "Debug")
  set(ENABLE_ASAN 1)
else()
  message(STATUS "Build type: Release")
  set(CMAKE_BUILD_TYPE "Release")
endif()

## Others
if (WIN32)
    message(STATUS "Platform: windows")
    add_definitions(-DUSE_WIN32DRV=1)
    set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT Simulator)
elseif (UNIX)
    message(STATUS "Platform: UNIX")

set(WARNING_FLAGS
    "-Wall -Wextra \
    -Wshadow -Wundef -Wmaybe-uninitialized -Wno-deprecated-declarations \
    -Wno-unused-function -Wno-error=strict-prototypes -Wpointer-arith -fno-strict-aliasing -Wno-error=cpp -Wuninitialized \
    -Wno-unused-parameter -Wno-missing-field-initializers -Wno-format-nonliteral -Wno-cast-qual -Wunreachable-code -Wno-switch-default  \
    -Wreturn-type -Wmultichar -Wformat-security -Wno-ignored-qualifiers -Wno-error=pedantic -Wno-sign-compare -Wno-error=missing-prototypes -Wdouble-promotion -Wclobbered -Wdeprecated  \
    -Wempty-body -Wshift-negative-value \
    -Wtype-limits -Wsizeof-pointer-memaccess -Wpointer-arith -Wno-cast-function-type -Wunused-but-set-variable")

set(WARNING_FLAGS "${WARNING_FLAGS} -Wno-shadow -Wno-unused-but-set-variable -Wno-double-promotion -Werror=narrowing -Werror=narrowing -Wunused-function")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${WARNING_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${WARNING_FLAGS}")

    # Address Sanitizer
    if (ENABLE_ASAN)
        set(ASAN_FLAGS "-fsanitize=address -fsanitize=undefined -fno-omit-frame-pointer -fsanitize=leak")
        message(STATUS "ASAN_FLAGS: ${ASAN_FLAGS}")
        add_definitions(${ASAN_FLAGS})
        set(CMAKE_EXE_LINKER_FLAGS ${CMAKE_EXE_LINKER_FLAGS} ${ASAN_FLAGS})
    endif ()

endif ()

# Sources
# file(GLOB EXAMPLE_SOURCES
#         ${PROJECT_SOURCE_DIR}/example/*.c
#         )

# glad
include_directories(
        ${PROJECT_SOURCE_DIR}/glad/include
        )
set(GLAD_SOURCES
        ${PROJECT_SOURCE_DIR}/glad/src/glad.c
        )

set(SOURCES
        ${GLAD_SOURCES}
        )

# Packages
cmake_policy(SET CMP0072 NEW)
find_package(OpenGL REQUIRED)
find_package(GLUT REQUIRED)
# find_package(GLEW REQUIRED)
find_package(glfw3 REQUIRED)

# Executable
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
add_executable(Simulator main.cpp ${SOURCES} ${INCLUDES})

# Link
target_link_libraries(
        Simulator PRIVATE
        OpenGL::GL
        GLUT::GLUT
        # GLEW::GLEW
        glfw
)

set_target_properties(
        Simulator
        PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${PROJECT_SOURCE_DIR}/build"
)
