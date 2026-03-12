cmake_minimum_required(VERSION 3.22.1)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 11)

project("${APP_LIB_NAME}")

# Submódulos
add_subdirectory(${CMAKE_SOURCE_DIR}/deps/raylib)
add_subdirectory(${CMAKE_SOURCE_DIR}/deps/raymob)

# Archivo principal de raymob (proporciona android_main)
set(RAYMOB_MAIN ${CMAKE_SOURCE_DIR}/deps/raymob/raymob.c)

# Fuentes del juego (desde PROJECT_ROOT/src)
file(GLOB_RECURSE GAME_SOURCES "../../../../../../src/*.cpp")

# Pegamento de Android
include_directories(${ANDROID_NDK}/sources/android/native_app_glue/)
set(ANDROID_GLUE ${ANDROID_NDK}/sources/android/native_app_glue/android_native_app_glue.c)

# Biblioteca nativa
add_library(${APP_LIB_NAME} SHARED ${GAME_SOURCES} ${ANDROID_GLUE} ${RAYMOB_MAIN})

# Definiciones
target_compile_definitions(${APP_LIB_NAME} PRIVATE PLATFORM_ANDROID)
if(CMAKE_BUILD_TYPE MATCHES "Debug")
    target_compile_definitions(${APP_LIB_NAME} PRIVATE _DEBUG DEBUG)
endif()

# Directorios de inclusión
target_include_directories(${APP_LIB_NAME} PRIVATE
    "${CMAKE_SOURCE_DIR}/deps/raylib"
    "${CMAKE_SOURCE_DIR}/deps/raymob"
    "../../../../../../src"   # para headers del juego
)

# Enlace
target_link_libraries(${APP_LIB_NAME} raylib raymoblib)