cmake_minimum_required(VERSION 3.23)

project(
        SpaceCrawler
        VERSION 0.0.1
        DESCRIPTION "Space visualization software"
        LANGUAGES CXX
)
set(CMAKE_CXX_STANDARD 20)

# Sources
file(GLOB_RECURSE SOURCES sources/*.cpp)
file(GLOB_RECURSE HEADERS sources/*.hpp sources/*.h)
file(GLOB_RECURSE SHADERS shader/*.hpp shader/*.h shader/*.cpp)

message("SHADERS FILES ARE ${SHADERS}")

# Build path

if (WIN32)
    set(PLATFORM windows)
    if(CMAKE_VERSION VERSION_LESS 3.15.0)
        set(_change_MSVC_flags TRUE)
    else()
        # Set MSVC runtime to MultiThreaded (/MT)
        cmake_policy(SET CMP0091 NEW)
        set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    endif()
endif(WIN32)

if (UNIX)
    set(PLATFORM unix)
endif (UNIX)

if(APPLE)
    set(PLATFORM apple)
endif()

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set(TARGET x64)
elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
    set(TARGET x86)
endif()

set(BUILD_PATH "${CMAKE_CURRENT_SOURCE_DIR}/bin/${PLATFORM}/${CMAKE_BUILD_TYPE}/${TARGET}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BUILD_PATH})

#OpenCl
find_package(OpenCL REQUIRED)
message(STATUS "OpenCL included at ${OpenCL_INCLUDE_DIR}")

#OpenGL
find_package(OpenGL REQUIRED)

add_definitions(-DGLEW_STATIC)
add_subdirectory(libraries/glfw EXCLUDE_FROM_ALL)
add_subdirectory(libraries/glew EXCLUDE_FROM_ALL)
add_subdirectory(libraries/glm EXCLUDE_FROM_ALL)


add_executable(${PROJECT_NAME} src/main.cpp ${SOURCES} ${HEADER} ${SHADERS})
target_include_directories(${PROJECT_NAME} PUBLIC libraries/imgui)
target_link_libraries( ${PROJECT_NAME} ${OPENGL_LIBRARIES} glfw glm libglew_static)


message(STATUS "Copy resources  ${CMAKE_CURRENT_SOURCE_DIR}/resources  to ${CMAKE_CURRENT_BINARY_DIR}/resources")
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND  ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/resources ${CMAKE_CURRENT_BINARY_DIR}/resources)
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND  ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/shaders ${CMAKE_CURRENT_BINARY_DIR}/shaders)

