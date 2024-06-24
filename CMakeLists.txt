cmake_minimum_required(VERSION 3.8)
project(voro)

set(CMAKE_BUILD_TYPE "RelWithDebInfo")

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -O3 -march=native)
endif()

# Find dependencies
find_package(ament_cmake REQUIRED)
find_package(ament_cmake_ros REQUIRED)

# voro library configuration
file(GLOB_RECURSE VORO_HEADERS src/voro++.hh)
file(GLOB VORO_SOURCES src/*.cc)
file(GLOB NOT_VORO_SOURCES src/v_base_wl.cc src/cmd_line.cc src/voro++.cc)
list(REMOVE_ITEM VORO_SOURCES ${NOT_VORO_SOURCES})
add_library(voro SHARED ${VORO_SOURCES})
target_compile_features(voro PUBLIC c_std_99 cxx_std_17)  # Require C99 and C++17
target_compile_definitions(voro PRIVATE "voro_BUILDING_LIBRARY")
target_include_directories(voro PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>)

# Library installation
install(
  FILES ${VORO_HEADERS}
  DESTINATION include
)
install(
  TARGETS voro
  EXPORT voroTargets
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_export_targets(voroTargets HAS_LIBRARY_TARGET)

ament_package()
