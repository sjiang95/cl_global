cmake_minimum_required(VERSION 3.20)

project(hello)
set(CMAKE_CXX_STANDARD 20) 

add_executable(hello helloworld.cpp)

target_link_libraries(hello)