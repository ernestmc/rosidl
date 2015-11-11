cmake_minimum_required(VERSION 2.8.3)

project(rosidl_generator_c)

find_package(ament_cmake REQUIRED)
find_package(ament_cmake_python REQUIRED)

ament_export_dependencies(rosidl_cmake)
ament_export_include_directories(include)

include_directories(include)

ament_python_install_package(${PROJECT_NAME})

if(AMENT_ENABLE_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()

  set(message_files
    "msg/Empty.msg"

    "msg/PrimitiveStaticArrays.msg"

    "msg/PrimitivesBounded.msg"
    "msg/PrimitivesStatic.msg"
    "msg/PrimitivesUnbounded.msg"

    "msg/BoundedArrayBounded.msg"
    "msg/BoundedArrayStatic.msg"
    "msg/BoundedArrayUnbounded.msg"

    "msg/StaticArrayBounded.msg"
    "msg/StaticArrayStatic.msg"
    "msg/StaticArrayUnbounded.msg"

    "msg/UnboundedArrayBounded.msg"
    "msg/UnboundedArrayStatic.msg"
    "msg/UnboundedArrayUnbounded.msg"
  )

  include(cmake/register_c.cmake)
  set(rosidl_generator_c_DIR "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

  rosidl_generator_c_extras(
    "${CMAKE_CURRENT_SOURCE_DIR}/bin/rosidl_generator_c"
    "${CMAKE_CURRENT_SOURCE_DIR}/rosidl_generator_c/__init__.py"
    "${CMAKE_CURRENT_SOURCE_DIR}/resource"
  )

  rosidl_generate_interfaces(${PROJECT_NAME} ${message_files}
    SKIP_INSTALL
  )

  if(NOT WIN32)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c99 -Wall -Wextra")
  endif()

  add_executable(test_interfaces_c "test/test_interfaces.c")
  add_dependencies(test_interfaces_c ${PROJECT_NAME} ${rosidl_generate_interfaces_TARGET})
  # include the built files directly, instead of their install location
  include_directories("${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}")
endif()

ament_package(
  CONFIG_EXTRAS "cmake/register_c.cmake" "rosidl_generator_c-extras.cmake.in"
)

install(
  PROGRAMS bin/rosidl_generator_c
  DESTINATION lib/rosidl_generator_c
)
install(
  DIRECTORY cmake resource
  DESTINATION share/${PROJECT_NAME}
)
install(
  DIRECTORY include/
  DESTINATION include
)
