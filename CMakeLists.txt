cmake_minimum_required(VERSION 3.10)
project(ddio_modify CXX)

set(CMAKE_CXX_STANDARD 17)

add_executable(ddio_modify
        ddio-modify.cpp)
target_link_libraries(ddio_modify -lpci -lgflags)
