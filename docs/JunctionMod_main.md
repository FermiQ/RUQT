# `JunctionMod/main.cpp`

## Overview

This C++ file, `main.cpp`, serves as the main entry point for the `JunctionMod` command-line executable. Its primary responsibilities are to parse command-line arguments, provide help information, and instantiate and run a `Job` object to process input script files (referred to as ".mm files").

## Key Components

-   **`void PrintHelp()`**:
    -   A function that prints a detailed help message to the console.
    -   The help message describes the various commands that can be used in a `JunctionMod` script file (`.mm` file), their syntax, and examples. Commands include `INIT`, `PRINT`, `SORT`, `ALIGN`, `DEVICE_SWITCH`, and various `*_ELEC` electrode generation commands.

-   **`int main(int argc, char **argv)`**:
    -   The standard C++ main function.
    -   **Argument Parsing**:
        -   Checks if any arguments are provided. If `argc == 1` (only the program name is given), it prints a brief usage message and instructions on how to get help, then exits.
        -   If arguments are provided, it takes the first argument (`argv[1]`) as a potential command or filename.
    -   **Help Command**:
        -   If the first argument is `"help"`, it calls `PrintHelp()` and then exits.
    -   **Job Execution**:
        -   If the first argument is not `"help"`, it assumes the arguments are `.mm` script files.
        -   Prints an ASCII art banner for "JunctionMod".
        -   Creates an instance of the `Job` class: `Job jobExecuter;`.
        -   Iterates through the command-line arguments (starting from `argv[1]`). For each argument, it calls `jobExecuter.run(argument)`, treating each argument as an input script file to be processed.
        -   After processing all input files, it prints a "MODIFICATION COMPLETED" message.
    -   Returns `0` on successful completion.

## Important Variables/Constants

-   `argc` (int): Argument count passed to `main`.
-   `argv` (char**): Array of argument strings passed to `main`.
-   `jobExecuter` (Job): An instance of the `Job` class that handles the actual processing of script files.
-   `argument` (std::string): Stores the current command-line argument being processed.

## Usage Examples

The `JunctionMod` executable is intended to be run from the command line:

1.  **Getting Help**:
    ```bash
    junctionmod help 
    ```
    This will display the detailed help information about available commands and their syntax.

2.  **Running a Script File**:
    ```bash
    junctionmod my_job_script.mm
    ```
    This will execute the commands listed in `my_job_script.mm`.

3.  **Running Multiple Script Files**:
    ```bash
    junctionmod script1.mm script2.mm
    ```
    This will execute `script1.mm` followed by `script2.mm`.

## Dependencies and Interactions

-   **`#include <cstdlib>`**: For `exit()`.
-   **`#include <iostream>`**: For standard console input/output (printing help, banners, status messages).
-   **`#include <cstring>`**: Likely for C-style string operations if they were used (though `std::string` is more prominent).
-   **`#include <vector>`**: Although not directly used in `main.cpp`'s logic, it's included, possibly as a common header or for potential future use.
-   **`"molecule.h"`**: Included because the `Job` class depends on `Molecule`.
-   **`"transform.h"`**: Included because the `Job` class depends on `Transform`.
-   **`"electrode.h"`**: Included because the `Job` class depends on `Electrode`.
-   **`"job.h"`**: Critically depends on the `Job` class, as `main`'s primary role is to instantiate and run a `Job`.
-   **File System**: Reads `.mm` script files provided as command-line arguments (delegated to `Job::run`).
-   **User Interaction**: Primarily through command-line arguments and console output for help and status messages. Further user interaction (prompts for input) is handled within the `Job` class methods.
```
