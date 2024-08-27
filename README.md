# logger.c
A simple logger in written in pure C.

## build steps

Clone:

```sh
git clone https://github.com/teleprint-me/logger.c logger
```

Build:

```sh
cmake -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build --config Debug -j 16
```

Run test (there's only a single, primitive, test at the moment):

```sh
./build/bin/test_logger
```

Use submodule for inclusion:

```sh
git submodule add https://github.com/teleprint-me/logger.c mods/logger
```

Use submodule to update:

```sh
git submodule update --init --recursive --remote mods/logger
```

Note that updating shouldn't be necessary for stable releases.

## usage example

```c
#include "logger.h"

#include <stdio.h>

int main(void) {
    const char*    file_path = "test.log";
    struct Logger* file_logger
        = logger_create(LOG_LEVEL_DEBUG, LOG_TYPE_FILE, file_path);
    LOG(file_logger,
        LOG_LEVEL_DEBUG,
        "Logging to a file: 1, 2, %d... Done!\n",
        3);

    // Clean up
    logger_free(file_logger);

    return 0;
}
```
