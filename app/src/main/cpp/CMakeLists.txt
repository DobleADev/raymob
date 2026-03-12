cmake_minimum_required(VERSION 3.22.1)

# Project name
project("game")

# Set C++ standard (match your project)
set(CMAKE_CXX_STANDARD 17)

# ============================================================
# 1. Include raylib (from submodule)
# ============================================================
add_subdirectory(deps/raylib)

# ============================================================
# 2. raymob helper sources (keep these)
# ============================================================
set(RAYMOB_SOURCES
    deps/raymob/callback.c
    deps/raymob/display.c
    deps/raymob/helper.c
    deps/raymob/sensor.c
    deps/raymob/soft_keyboard.c
    deps/raymob/vibrator.c
    # Add any other .c files from deps/raymob/ (but NOT example/)
)

# ============================================================
# 3. YOUR GAME SOURCES (adjust paths to your project root)
# ============================================================
# Assume the project root is 6 levels up from this file:
#   android/raymob/app/src/main/cp p/CMakeLists.txt
#   -> ../../../../../../../   (goes to repo root)
set(PROJECT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/../../../../../../../)

file(GLOB_RECURSE GAME_SOURCES
    "${PROJECT_ROOT}/src/*.cpp"
    "${PROJECT_ROOT}/app/*.cpp"
)

# If you have any .c files, add them as well:
# file(GLOB_RECURSE GAME_C_SOURCES "${PROJECT_ROOT}/src/*.c")
# list(APPEND GAME_SOURCES ${GAME_C_SOURCES})

# ============================================================
# 4. Create the shared library
# ============================================================
add_library(game SHARED
    ${RAYMOB_SOURCES}
    ${GAME_SOURCES}
)

# Include directories
target_include_directories(game PRIVATE
    ${PROJECT_ROOT}/src          # for your own headers
    deps/raylib/src              # raylib headers
    deps/raymob                  # for raymob.h
)

# Link libraries
target_link_libraries(game
    raylib
    log   # Android logging
    z     # compression (if needed)
    GLESv3
    EGL
    android
    OpenSLES
)

# Define Android platform (optional, already handled by Gradle)
target_compile_definitions(game PRIVATE
    PLATFORM_ANDROID
    # Add any other macros your code expects, e.g.:
    # ANDROID
)