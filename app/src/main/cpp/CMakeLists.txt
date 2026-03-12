# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Set the minimum required version of CMake
cmake_minimum_required(VERSION 3.22.1)

# Set standards for C and C++
set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 11)

# Set the project name based on the name given on the gradle.properties
project("${APP_LIB_NAME}")

# Include raylib and raymob as a subdirectories
add_subdirectory(${CMAKE_SOURCE_DIR}/deps/raylib)
add_subdirectory(${CMAKE_SOURCE_DIR}/deps/raymob)

# Recolectar fuentes del juego (desde PROJECT_ROOT/src)
# Ajusta la cantidad de ".." si la estructura real es diferente:
# desde .../android/raymob/app/src/main/cpp hasta PROJECT_ROOT/src = 6 niveles
file(GLOB_RECURSE GAME_SOURCES "../../../../../../src/*.cpp")

# Pegamento de Android
include_directories(${ANDROID_NDK}/sources/android/native_app_glue/)
set(ANDROID_GLUE ${ANDROID_NDK}/sources/android/native_app_glue/android_native_app_glue.c)

# Crear la biblioteca nativa con los fuentes del juego y el glue
add_library(${APP_LIB_NAME} SHARED ${GAME_SOURCES} ${ANDROID_GLUE})

# Definiciones comunes
target_compile_definitions(${APP_LIB_NAME} PRIVATE PLATFORM_ANDROID)
if(CMAKE_BUILD_TYPE MATCHES "Debug")
    target_compile_definitions(${APP_LIB_NAME} PRIVATE _DEBUG DEBUG)
endif()

# Directorios de inclusión
target_include_directories(${APP_LIB_NAME} PRIVATE
    "${CMAKE_SOURCE_DIR}/deps/raylib"
    "${CMAKE_SOURCE_DIR}/deps/raymob"
    "../../../../../../src"   # Para que los fuentes encuentren sus headers
)

# Enlazar con raylib y raymoblib
target_link_libraries(${APP_LIB_NAME} raylib raymoblib)