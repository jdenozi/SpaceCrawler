cmake_minimum_required(VERSION 3.0.0)

project(
        SpaceCrawler
        VERSION 0.0.1
        DESCRIPTION "Space visualization software"
        LANGUAGES CXX
)

link_directories(libs/glfw)

include_directories(libs/glad)
include_directories(libs/KHR)
include_directories(libs/imgui)
include_directories(libs/glfw)
include_directories(include)

file(GLOB_RECURSE SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
add_executable(${PROJECT_NAME} ${SOURCES})

find_package(OpenGL REQUIRED)
target_link_libraries(${PROJECT_NAME} PRIVATE ${OPENGL_gl_LIBRARY})
target_link_libraries(${PROJECT_NAME} PRIVATE glfw3)

file(COPY resources DESTINATION /)

