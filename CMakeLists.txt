cmake_minimum_required(VERSION 3.21)
project(YourProjectName LANGUAGES CXX)

# Fetch and setup SFML
include(FetchContent)
FetchContent_Declare(SFML
        GIT_REPOSITORY https://github.com/SFML/SFML.git
        GIT_TAG 2.6.x)
FetchContent_MakeAvailable(SFML)

# Add your main executable
file(GLOB_RECURSE PROJECT_SOURCES
        "${CMAKE_SOURCE_DIR}/src/*.cpp"
        "${CMAKE_SOURCE_DIR}/src/*.h")
add_executable(YourProjectName ${PROJECT_SOURCES})

# Copy resources
add_custom_command(TARGET YourProjectName POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${CMAKE_SOURCE_DIR}/res"
        $<TARGET_FILE_DIR:YourProjectName>/res
)
target_link_libraries(YourProjectName PRIVATE sfml-graphics sfml-audio)
target_compile_features(YourProjectName PRIVATE cxx_std_17)

# Windows specific options
if (WIN32 AND BUILD_SHARED_LIBS)
    add_custom_command(TARGET YourProjectName POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_RUNTIME_DLLS:YourProjectName> $<TARGET_FILE_DIR:YourProjectName> COMMAND_EXPAND_LISTS)
endif()

# Add libraries
# B2D library
file(GLOB_RECURSE B2D_SOURCES
        "${CMAKE_SOURCE_DIR}/lib/b2d/*.cpp"
        "${CMAKE_SOURCE_DIR}/lib/b2d/*.h")
add_library(lib_b2d STATIC ${B2D_SOURCES})
target_include_directories(lib_b2d INTERFACE "${CMAKE_SOURCE_DIR}/lib/b2d")

# Engine
file(GLOB_RECURSE ENGINE_SOURCES
        "${CMAKE_SOURCE_DIR}/engine/*.cpp"
        "${CMAKE_SOURCE_DIR}/engine/*.h")
add_library(lib_engine STATIC ${ENGINE_SOURCES})
target_include_directories(lib_engine INTERFACE "${CMAKE_SOURCE_DIR}/engine")

# ECM library
file(GLOB_RECURSE ECM_SOURCES
        "${CMAKE_SOURCE_DIR}/lib/ecm/*.cpp"
        "${CMAKE_SOURCE_DIR}/lib/ecm/*.h")
add_library(lib_ecm STATIC ${ECM_SOURCES})
target_include_directories(lib_ecm INTERFACE "${CMAKE_SOURCE_DIR}/lib/ecm")

# Maths library
add_library(lib_maths INTERFACE)
target_sources(lib_maths INTERFACE "${CMAKE_SOURCE_DIR}/lib/maths/maths.h")
target_include_directories(lib_maths INTERFACE "${CMAKE_SOURCE_DIR}/lib/maths")

# Tile level loader library
file(GLOB_RECURSE TILE_LOADER_SOURCES
        "${CMAKE_SOURCE_DIR}/lib/tile_level_loader/*.cpp"
        "${CMAKE_SOURCE_DIR}/lib/tile_level_loader/*.h")
add_library(lib_tile_level_loader STATIC ${TILE_LOADER_SOURCES})
target_include_directories(lib_tile_level_loader INTERFACE "${CMAKE_SOURCE_DIR}/lib/tile_level_loader")

# World gen library (add similar block if needed)

# Linking libraries
target_link_libraries(YourProjectName PRIVATE sfml-graphics sfml-audio lib_engine lib_b2d lib_ecm lib_maths lib_tile_level_loader)

# Installation (if needed)
install(TARGETS YourProjectName)

