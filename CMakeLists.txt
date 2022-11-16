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

find_package(OpenCL REQUIRED)


set(BUILD_PATH "${CMAKE_CURRENT_SOURCE_DIR}/bin/${PLATFORM}/${CMAKE_BUILD_TYPE}/${TARGET}")

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BUILD_PATH})

add_executable(SpaceCrawler main.cpp ${SOURCES} ${HEADER})
