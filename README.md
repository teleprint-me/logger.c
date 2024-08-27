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

_Note_: Updating is generally unnecessary for stable releases.

## Usage Example

```c
#include "logger.h"

#include <stdio.h>

int main(void) {
    const char* file_path = "test.log";
    struct Logger* file_logger = logger_create(LOG_LEVEL_DEBUG, LOG_TYPE_FILE, file_path);

    LOG(file_logger, LOG_LEVEL_DEBUG, "Logging to a file: 1, 2, %d... Done!\n", 3);

    // Clean up
    logger_free(file_logger);

    return 0;
}
```

## API Documentation

### Logger Creation

- **`struct Logger* logger_create(int level, int type, const char* file_path);`**
  - **`level`**: The minimum log level (e.g., `LOG_LEVEL_DEBUG`,
    `LOG_LEVEL_INFO`).
  - **`type`**: The output type (e.g., `LOG_TYPE_CONSOLE`, `LOG_TYPE_FILE`).
  - **`file_path`**: Path to the log file (if `LOG_TYPE_FILE` is used).

### Logging Messages

- **`void LOG(struct Logger* logger, int level, const char* format, ...);`**
  - Logs a formatted message with the specified level.

### Logger Cleanup

- **`void logger_free(struct Logger* logger);`**
  - Frees resources associated with the logger.

## Testing

The provided test is simple and currently not automated. It prints log messages
to verify the output visually. Here’s how it works:

1. **Global Logger Messages**: Logs a warning and an error.
2. **Lazy Logger Messages**: Logs a debug and an error.
3. **Conditional Logger Messages**: Logs info and error if certain conditions
   are met.

While it’s good to see passing tests, automated testing would strengthen
confidence in the logger's reliability. This might involve adding assertions to
check the contents of the log files or intercepting stdout/stderr to validate
console outputs.

## Future Improvements

- **Automated Testing**: Integrate assertions and expand the test suite to
  cover edge cases.
- **Thread Safety**: Consider implementing thread safety for concurrent
  logging.
- **Configurable Log Format**: Allow customization of the log message format.

## License

This project is licensed under the AGPL License - see the [LICENSE](LICENSE)
file for details.
