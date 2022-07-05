cmake_minimum_required(VERSION 3.10)
project(oculus_driver VERSION 0.1)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -ggdb3)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(Boost COMPONENTS system thread REQUIRED)
message(STATUS "Boost version  : ${Boost_VERSION}")
message(STATUS "Boost location : ${Boost_INCLUDE_DIRS}")

list(APPEND narval_oculus_headers
    include/narval_oculus/Oculus.h
    include/narval_oculus/utils.h
    include/narval_oculus/print_utils.h
    include/narval_oculus/Clock.h
    include/narval_oculus/CallbackQueue.h
    include/narval_oculus/StatusListener.h
    include/narval_oculus/SonarClient.h
    include/narval_oculus/SonarDriver.h
    include/narval_oculus/AsyncService.h
)

add_library(narval_oculus SHARED
    src/print_utils.cpp
    src/StatusListener.cpp
    src/SonarClient.cpp
    src/SonarDriver.cpp
    src/AsyncService.cpp
)
set_target_properties(narval_oculus PROPERTIES
    PUBLIC_HEADER "${narval_oculus_headers}"
    INSTALL_RPATH_USE_LINK_PATH True
)
target_include_directories(narval_oculus PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
target_link_libraries(narval_oculus PUBLIC
    Boost::system
    Boost::thread
)
target_compile_features(narval_oculus PUBLIC cxx_std_17)

# include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/narval_installation.cmake)
# narval_install_target(narval_oculus)

# add_subdirectory(tools)
add_subdirectory(tests)

install(DIRECTORY include/narval_oculus/ DESTINATION include/${PROJECT_NAME})
install(TARGETS narval_oculus
    EXPORT narval_oculus
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)

ament_export_include_directories(include)
ament_export_libraries(
    narval_oculus
)
ament_export_targets(
    narval_oculus
)

ament_package()


