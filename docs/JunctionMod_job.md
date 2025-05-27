# `JunctionMod/job.h`

## Overview

The `job.h` header file defines the `Job` class for the `JunctionMod` library. This class is designed to manage and execute a series of molecular modeling tasks based on commands, likely read from a script file. It maintains a collection of named `Molecule` objects and uses `Transform` and `Electrode` objects to perform operations on them.

## Key Components

-   **`class Job`**:
    -   **Private Members**:
        -   **`struct Molecules`**: A nested structure to associate a `Molecule` object with a string `name`.
            -   `Molecule jobMole`: The actual molecule object.
            -   `std::string name`: The user-defined name for this molecule.
        -   `std::vector<Molecules> moleVec`: A vector to store multiple named `Molecule` objects that the job can operate on.
        -   `Transform TF`: An instance of the `Transform` class, used for geometric transformations.
        -   `Electrode ELE`: An instance of the `Electrode` class, used for adding electrodes to molecules.
        -   **Command Methods (private)**: These methods correspond to commands that can be issued to the `Job` instance (likely parsed from an input script by the public `run` method).
            -   `void PresentSelection(std::string moleName)`: Displays the atoms of a specified molecule to the console.
            -   `void INIT(std::string filename, std::string filetype, std::string moleName)`: Initializes a new `Molecule` object by loading data from `filename` (e.g., an XYZ file) and stores it with `moleName`.
            -   `void PRINT(std::string filename, std::string filetype, std::string moleName)`: Saves the specified `Molecule` to `filename` in the given `filetype` (e.g., XYZ, GAMESS).
            -   `void SORT(std::string moleName, std::string axis)`: Sorts the atoms of the specified `Molecule` based on their coordinates along the given `axis`.
            -   `void ALIGN(std::string moleName, std::string STRaxis)`: Aligns the specified `Molecule` based on user input for selecting atoms to define the alignment axis.
            -   `void DEVICE_SWITCH(std::string moleName, std::string angleDisp, std::string angleInc)`: Performs a series of rotations (device switch operation) on a part of the molecule, saving intermediate geometries. Takes user input for defining rotation axes and affected atoms.
            -   `void HYDROREP_ELECTRODE(std::string moleName)`: Adds electrodes by replacing existing hydrogen atoms. Takes user input for parameters.
            -   `void TIPSQUARE_ELECTRODE(std::string moleName)`: Adds electrodes with square tips. Takes user input.
            -   `void PYRAMID_ELECTRODE(std::string moleName)`: Adds electrodes with pyramid tips. Takes user input.
            -   `void LINEAR_ELECTRODE(std::string moleName)`: Adds linear electrodes. Takes user input.
            -   `void CUSTOM_ELECTRODE(std::string juncName, std::string elecName)`: Attaches a custom electrode (`elecName`) to a junction molecule (`juncName`). Takes user input.
            -   `void ADDCUSTOM_ELECTRODE(std::string juncName, std::string elecName, std::string elecName2)`: Attaches two different custom electrodes to a junction molecule. Takes user input.
    -   **Public Members (Method)**:
        -   `void run(std::string mmFile)`: This is the main public interface of the `Job` class. It reads commands from the specified `mmFile` (MoleculeMod file), parses them, and calls the corresponding private command methods to execute the job.

## Important Variables/Constants

-   `moleVec`: The vector storing all molecules being worked on. This is central to the `Job`'s state.
-   `TF`: The `Transform` object instance.
-   `ELE`: The `Electrode` object instance.
-   The parameters to the private methods (e.g., `moleName`, `filename`, `filetype`, `axis`, `angleDisp`) are crucial for defining the operations. These are typically parsed from the input script file within the `run` method.

## Usage Examples

The `Job` class is intended to be used by the `main` function of `JunctionMod`. The `main` function would create a `Job` object and then call its `run` method, passing the name of a script file.

```cpp
// In main.cpp
#include "job.h"
// ...

int main(int argc, char **argv) {
    // ... (argument parsing) ...
    if (/* arguments are valid for running a job file */) {
        Job jobExecuter;
        std::string job_script_file = argv[1]; // Assuming script file is the first argument
        jobExecuter.run(job_script_file);
    }
    // ...
    return 0;
}
```

An example `mmFile` (e.g., `myjob.mm`) processed by `jobExecuter.run("myjob.mm")` might look like:

```
INIT input_molecule.xyz xyz main_mol
L_ELEC main_mol  # This would then prompt for details for linear electrode
PRINT output_molecule.xyz xyz main_mol
```

## Dependencies and Interactions

-   **`#include <iostream>`**: For console input/output, especially in methods that prompt the user (e.g., `ALIGN`, `*_ELECTRODE` methods).
-   **`#include <cstring>`**: For C-style string operations (e.g., `strcpy`, `strtok` used in the `.cpp` for parsing).
-   **`#include <vector>`**: For using `std::vector` to store `Molecules`.
-   **`"molecule.h"`**: Depends on the `Molecule` class to define and manage molecular structures.
-   **`"transform.h"`**: Depends on the `Transform` class for performing geometric transformations.
-   **`"electrode.h"`**: Depends on the `Electrode` class for constructing and attaching electrodes.
-   **File System**: The `run` method reads a command script file. The `INIT` and `PRINT` methods read and write molecule files.
-   The class interacts heavily with the user via the console for many of its operations, prompting for atom indices, bond lengths, etc.
```
