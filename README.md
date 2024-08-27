# logger.c

A simple logger written in pure C.

## Overview

`logger.c` provides a minimal logging library that allows you to log messages
with different severity levels (e.g., DEBUG, INFO, WARN, ERROR) to various
outputs like console or file. It's designed to be lightweight and easily
integrated into existing C projects.

## Build Instructions

### Clone the Repository

Clone the repository from GitHub:

```sh
git clone https://github.com/teleprint-me/logger.c logger
```

### Build with CMake

Use CMake to configure and build the project:

```sh
cmake -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build --config Debug -j 16
```

### Run Tests

Run the provided test:

```sh
./build/bin/test_logger
```

### Use as a Submodule

To include `logger.c` in your project as a submodule:

```sh
git submodule add https://github.com/teleprint-me/logger.c mods/logger
```

Update the submodule:

```sh
git submodule update --init --recursive --remote mods/logger
```

## API Documentation

### Logger Levels

The logger supports four logging levels:

- **`LOG_LEVEL_DEBUG`**: For detailed debugging information.
- **`LOG_LEVEL_INFO`**: For general informational messages.
- **`LOG_LEVEL_WARN`**: For warnings that indicate a potential issue.
- **`LOG_LEVEL_ERROR`**: For errors that represent a failure in a particular
  operation.

### Logger Types

The logger can output logs in two different ways:

- **`LOG_TYPE_STREAM`**: Logs are written to a stream (e.g., `stdout` or
  `stderr`).
- **`LOG_TYPE_FILE`**: Logs are written to a specified file.

### Logger Structure

The core of the logging system is the `Logger` structure:

```c
logger_t {
    /**< The logging level of the logger. */
    log_level_t log_level;
    /**< The type of logger (stream or file). */
    log_type_t  log_type;
    /**< The name associated with the logger type. */
    const char* log_type_name;
    /**< The file stream for writing log messages. */
    FILE* file_stream;
    /**< The path to the log file. */
    const char* file_path;
    /**< Mutex to ensure thread-safe logging. */
    pthread_mutex_t thread_lock;
};
```

### Functions

#### logger_create

```c
logger_t* logger_create(
  log_level_t log_level,
  log_type_t log_type,
  const char* file_path
)
```

Creates a new logger instance:

- **Parameters**:
  - `log_level`: The desired logging level.
  - `log_type`: The type of logger.
  - `file_path`: The path to the log file (can be `NULL` to use `stderr`).
- **Returns**: A pointer to the newly created `Logger` instance, or `NULL` if
  an error occurs.

#### logger_free

```c
bool logger_free(logger_t* logger)
```

Destroys a logger instance and releases associated resources:

- **Parameters**:
  - `logger`: A pointer to the `Logger` instance to be destroyed.
- **Returns**: `true` if the logger was successfully destroyed, `false`
  otherwise.

#### logger_message

```c
bool logger_message(
  logger_t* logger,
  log_level_t log_level,
  const char* format,
  ...
)
```

Logs a message with the specified log level to the logger's output:

- **Parameters**:

  - `logger`: A pointer to the `Logger` instance.
  - `log_level`: The log level of the message.
  - `format`: The format string of the message to be logged, followed by any
    additional arguments.

- **Returns**: `true` if the message was successfully logged, `false`
  otherwise.

#### LOG

```c
#define LOG(logger, level, format, ...)
```

A macro for logging messages using a logger instance:

- **Parameters**:
  - `logger`: A pointer to the `Logger` instance.
  - `level`: The log level of the message.
  - `format`: The format string of the message, followed by any additional
    arguments.

### Example Usage

Here's a basic example of how to use the logger:

```c
#include "logger.h"

int main(void) {
    const char* file_path = "test.log";
    logger_t* file_logger = logger_create(LOG_LEVEL_DEBUG, LOG_TYPE_FILE, file_path);

    LOG(file_logger, LOG_LEVEL_DEBUG, "Logging to a file: 1, 2, %d... Done!\n", 3);

    // Clean up
    logger_free(file_logger);

    return 0;
}
```

### Notes for Future Improvement

- **Testing**: The current testing framework is minimal. Consider automating
  and expanding the test suite to cover more edge cases, including
  multithreaded scenarios.
- **Error Handling**: While the logger has basic error handling, it might be
  worth revisiting the way errors are propagated, especially in a multithreaded
  context.
- **Performance**: Review the performance of logging under high concurrency to
  ensure the mutex implementation doesn't become a bottleneck.
- **File Rotation**: Implementing log file rotation could be a useful feature
  for long-running applications.
- **Logger Configuration**: Consider adding support for logger configuration
  via a file or environment variables to allow more flexible usage.

## License

This project is licensed under the AGPL License - see the [LICENSE](LICENSE)
file for details.
