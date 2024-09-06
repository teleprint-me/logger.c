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

Configure the project:

```sh
cmake -B build -DCMAKE_BUILD_TYPE=Debug
```

Build the configured project:

```sh
cmake --build build --config Debug -j $(nproc)
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

The `Logger` structure is the core of the logging system, encapsulating all the
necessary information for logging operations:

```c
typedef struct Logger {
    log_level_t log_level;
    log_type_t  log_type;
    const char* log_type_name;
    FILE* file_stream;
    const char* file_path;
    pthread_mutex_t thread_lock;
} logger_t;
```

#### Member Variables:

- **`log_level`**: Specifies the minimum severity level of messages that will
  be logged. Messages below this level are ignored.
- **`log_type`**: Defines the output destination for log messages. Options
  include:
  - `LOG_TYPE_STREAM`: Logs are sent to a stream such as `stdout` or `stderr`.
  - `LOG_TYPE_FILE`: Logs are written to a file specified by `file_path`.
- **`log_type_name`**: A descriptive name for the logger's output type, aiding
  in debugging and configuration management.
- **`file_stream`**: A pointer to the `FILE` object where log messages will be
  written. This could be a standard stream (`stdout`, `stderr`) or a file
  opened for writing.
- **`file_path`**: The path to the log file, if `LOG_TYPE_FILE` is selected. If
  logging to a stream, this can be `NULL`.
- **`thread_lock`**: A mutex that ensures that logging operations are
  thread-safe, preventing data races when multiple threads attempt to log
  messages simultaneously.

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

A macro for logging messages using a logger instance:

```c
#define LOG(logger, level, format, ...)
```

- **Parameters**:
  - `logger`: A pointer to the `Logger` instance.
  - `level`: The log level of the message.
  - `format`: The format string of the message, followed by any additional
    arguments.
  - `...` Additional arguments for formatting the message (optional).

#### LOG_DEBUG, LOG_INFO, LOG_WARN, and LOG_ERROR

Helper macro's to reduce boilerplate code for enhanced user-friendliness:

```c
#define LOG_DEBUG(format, ...)
#define LOG_INFO(format, ...)
#define LOG_WARN(format, ...)
#define LOG_ERROR(format, ...)
```

- **Parameters**:
  - `format` The format string for the log message.
  - `...` Additional arguments for formatting the message (optional).

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
