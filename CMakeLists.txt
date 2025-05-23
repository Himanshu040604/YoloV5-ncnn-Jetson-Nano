cmake_minimum_required(VERSION 3.5)
project(yolov5_ncnn_demo)

# C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find and enable OpenMP
find_package(OpenMP REQUIRED)
if(OpenMP_CXX_FOUND)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

# Find OpenCV
find_package(OpenCV REQUIRED)

# Include directories: OpenCV, NCNN, and standard system paths
include_directories(
  ${OpenCV_INCLUDE_DIRS}
  /usr/include/opencv4
  /usr/include
  /usr/local/include/ncnn
)

# Where you will find NCNN lib
link_directories(/usr/local/lib)

# Build the executable
add_executable(yolov5_ncnn
  yolov5.cpp
)

# Link libraries: OpenCV, NCNN, pthread, gomp (OpenMP runtime)
target_link_libraries(yolov5_ncnn
  ${OpenCV_LIBS}
  ncnn
  pthread
  gomp
)
