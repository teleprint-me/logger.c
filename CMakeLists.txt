cmake_minimum_required(VERSION 3.14)
project(logger VERSION 0.1.0 DESCRIPTION "ANSI C library for Logging")

# Set C standard rules
set(CMAKE_C_STANDARD 17)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON)

option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

# Main library
add_library(
    logger
    src/logger.c
)

set_target_properties(
    logger # [<targets>]...
    PROPERTIES # PROPERTIES [<prop1> <value1>]...
    VERSION ${PROJECT_VERSION}
    PUBLIC_HEADER include/logger.h
)

target_include_directories(logger PUBLIC include)

# Set the output directory for built binaries
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib)

# Add test executables
add_executable(test_logger tests/test_logger.c)

# Link test executables
target_link_libraries(test_logger m pthread logger)

# Set the output directory for the test executables
set_target_properties(
    test_logger # [<targets>]...
    PROPERTIES # PROPERTIES [<prop1> <value1>]...
    RUNTIME_OUTPUT_DIRECTORY ${EXECUTABLE_OUTPUT_PATH}
)

# Install rules for the library
install(
    TARGETS logger
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    PUBLIC_HEADER DESTINATION include
)

# Add custom targets to run tests
#
# NOTE: Custom targets do not seem to be able to be simplified
# due to the nature of their specificity.
add_custom_target(
    run_test_logger
    COMMAND ${EXECUTABLE_OUTPUT_PATH}/test_logger
    DEPENDS test_logger
    COMMENT "Running tests for logger"
)

enable_testing()
add_test(
    NAME test_logger
    COMMAND ${EXECUTABLE_OUTPUT_PATH}/test_logger
)
