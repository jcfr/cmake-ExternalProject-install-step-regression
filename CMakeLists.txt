cmake_minimum_required(VERSION 3.22)

project(Test)

add_custom_target(build ALL
  COMMAND ${CMAKE_COMMAND} -E echo "Building project..."
  COMMENT "This custom target is always expected to be built"
  )

install(CODE "message(\"Installing project...\")")

