cmake_minimum_required(VERSION 3.5.2 FATAL_ERROR)

set(LBFGS_MAJOR_VERSION 1)
set(LBFGS_MINOR_VERSION 0)
set(LBFGS_PATCH_VERSION 0)
set(LBFGS_VERSION ${LBFGS_MAJOR_VERSION}.${LBFGS_MINOR_VERSION}.${LBFGS_PATCH_VERSION})
project(lbfgs VERSION ${LBFGS_VERSION} LANGUAGES C)

add_library(lbfgs STATIC lib/lbfgs.c)
set_property(TARGET lbfgs PROPERTY POSITION_INDEPENDENT_CODE ON)
set(HEADER_FILES
  include/lbfgs.h
)
set_target_properties(lbfgs
  PROPERTIES
  POSITION_INDEPENDENT_CODE ON
  PUBLIC_HEADER "${HEADER_FILES}"
  )
install(TARGETS lbfgs EXPORT lbfgs-targets
  RUNTIME DESTINATION bin COMPONENT bin
  ARCHIVE DESTINATION lib COMPONENT dev
  LIBRARY DESTINATION lib COMPONENT shlib
  PUBLIC_HEADER DESTINATION include COMPONENT dev)

export(TARGETS lbfgs FILE "${PROJECT_BINARY_DIR}/lbfgs-targets.cmake")
export(PACKAGE lbfgs)

# Create the Config.cmake and ConfigVersion.cmake files
get_filename_component(CMAKE_INSTALL_INCLUDEDIR_ABS include REALPATH BASE_DIR "${CMAKE_INSTALL_PREFIX}")
set(CONF_INCLUDE_DIRS ${CMAKE_INSTALL_INCLUDEDIR_ABS})
configure_file(lbfgsConfig.cmake.in "cmake/lbfgsConfig.cmake" @ONLY)
configure_file(lbfgsConfigVersion.cmake.in "cmake/lbfgsConfigVersion.cmake" @ONLY)
install(FILES "${PROJECT_BINARY_DIR}/cmake/lbfgsConfig.cmake" "${PROJECT_BINARY_DIR}/cmake/lbfgsConfigVersion.cmake" DESTINATION "${CMAKE_INSTALL_PREFIX}/lib/cmake/lbfgs" COMPONENT dev)
set(CONF_INCLUDE_DIRS "${PROJECT_SOURCE_DIR}/include" "${PROJECT_BINARY_DIR}")
configure_file(lbfgsConfig.cmake.in "lbfgsConfig.cmake" @ONLY)
configure_file(lbfgsConfigVersion.cmake.in "${PROJECT_BINARY_DIR}/lbfgsConfigVersion.cmake" @ONLY)

add_executable(sample sample/sample.c)
target_link_libraries(sample PRIVATE lbfgs m)
target_include_directories(sample PRIVATE ${CONF_INCLUDE_DIRS})
target_include_directories(lbfgs PRIVATE ${CONF_INCLUDE_DIRS})
