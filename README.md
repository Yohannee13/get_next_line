*This project has been created as part of the 42 curriculum by yoandria.*

## Description

The `get_next_line` project focuses on creating a function in C from the ground up that can continuously read from a file descriptor (`fd`) and return one line at a time. The project is an essential milestone designed to build foundational knowledge around memory management, buffer manipulation, static variables, and understanding read loop lifecycles inside operating systems. The implementation seamlessly handles input parsing by grabbing text chunks until a `\n` or `EOF` is reached, averting the necessity of pulling the whole file into active memory.

## Instructions

**Compilation:**
To compile `get_next_line` together with your application, include the required `.c` files and compile them with your compiler flags. A special macro `-D BUFFER_SIZE=n` determines exactly how many bytes `read()` will sequentially request from the OS:

```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 main.c get_next_line.c get_next_line_utils.c
```

*(If `-D BUFFER_SIZE` is omitted during compilation, the code guarantees stability by resorting to a default size of 42).*

**Usage Example:**

```c
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include "get_next_line.h"

int main(void)
{
    int fd = open("test.txt", O_RDONLY);
    char *line;

    if (fd == -1)
        return (1);

    while ((line = get_next_line(fd)) != NULL)
    {
        printf("%s", line);
        free(line);
    }
    close(fd);
    return (0);
}
```

## Algorithm Explanation & Justification

The core functionality of this `get_next_line` employs a robust state-machine loop heavily dependent on a simple `static char *` variable to persist untampered data between subsequent function calls.

1. **Reading Loop:** The logic runs `read()` continuously fetching `BUFFER_SIZE` bytes into a dynamically allocated temporary buffer.
2. **Buffer Appending:** We cannot guess how far away a newline might be, so any successfully read buffer chunk is appended dynamically to the static string (with strict usage of an explicit duplicate-and-free protocol ensuring the old static memory is released without leaking).
3. **Extraction & Slicing:** As soon as `ft_strchr()` finds a newline `\n` mapped in the aggregated static string (or `EOF` forces `read()` to return 0), the loop resolves. The program calculates exactly how wide the line segment spans up to `\n` and exports that specific chunk.
4. **Residue Shifting:** The leftover trailing character residue remaining inherently after the parsed `\n` is preserved back into the static pointer for the next sequential function invocation.

**Justification:** This approach gracefully resolves buffer overflows and validates execution irrespective of the designated `BUFFER_SIZE`—handling bounds varying chaotically between 1, 42, up to 10000000. It adheres fully to the limitation parameters since it reads iteratively while conserving string traces perfectly utilizing heap allocations.

## Resources

- [Linux man-pages: read(2)](https://man7.org/linux/man-pages/man2/read.2.html)
- [Understanding Static Variables in C](https://www.geeksforgeeks.org/static-variables-in-c/)
- **AI Usage:** AI assistant was used to analyze memory leak risks involving aggressive `BUFFER_SIZE` scaling, assess debuggin strategies, and optimize the code for better performance and readability.
