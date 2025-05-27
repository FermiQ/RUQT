# `JunctionMod/molecule.h`

## Overview

The `molecule.h` header file defines the `Molecule` class, which is a fundamental component of the `JunctionMod` library. This class encapsulates the representation of a molecule, including its atomic composition and coordinates. It provides methods for initializing a molecule from a file, printing its information, accessing and modifying atomic data, and performing simple structural manipulations like adding, removing, or sorting atoms.

## Key Components

-   **`class Molecule`**:
    -   **Private Members**:
        -   `int atomCount`: Stores the total number of atoms in the molecule.
        -   **`struct atom`**: A nested structure to represent a single atom.
            -   `std::string atomSym`: The atomic symbol (e.g., "H", "C", "Au").
            -   `double coord[3]`: An array storing the X, Y, and Z coordinates of the atom.
        -   `std::vector<atom> atomVec`: A vector that stores all `atom` objects constituting the molecule.
        -   `void SwapStruct(atom& atom1, atom& atom2)`: A utility function to swap the data of two `atom` structures. Used by `SortByCoord`.
        -   `double GetAtomNum(std::string sym)`: A utility function to get the atomic number corresponding to an atomic symbol. Used by `PrintInfo` for GAMESS format.
    -   **Public Members (Methods)**:
        -   `void Initiate(std::string filename, std::string filetype)`: Initializes the molecule by reading atomic data from a specified `filename`. Currently supports "xyz" file format.
        -   `void PrintInfo(std::string filename, std::string filetype)`: Prints the molecule's atomic information to a specified `filename`. Supports "xyz", "gamess", and "maple" file formats.
        -   `void SetAtomCoord(int ithAtom, char toEdit, double coordValue)`: Sets the X, Y, or Z coordinate (`toEdit` specifies 'x', 'y', or 'z') of the `ithAtom` to `coordValue`.
        -   `void SetAtomSym(int ithAtom, std::string newAtom)`: Sets the atomic symbol of the `ithAtom` to `newAtom`.
        -   `double GetAtomCoord(int ithAtom, char toGet)`: Returns the X, Y, or Z coordinate (`toGet` specifies 'x', 'y', or 'z') of the `ithAtom`.
        -   `std::string GetAtomSym(int ithAtom)`: Returns the atomic symbol of the `ithAtom`.
        -   `void AddAtom(std::string AtSym, double coordX, double coordY, double coordZ)`: Adds a new atom with the given symbol and coordinates to the molecule. Increments `atomCount`.
        -   `void RemoveAtom(int ithAtom)`: Removes the `ithAtom` from the molecule. Decrements `atomCount`.
        -   `int GetAtomCount()`: Returns the current number of atoms in the molecule.
        -   `void SortByCoord(char axis)`: Sorts the atoms in `atomVec` based on their coordinate along the specified `axis` ('x', 'y', or 'z').

## Important Variables/Constants

-   `atomCount` (int): Tracks the number of atoms.
-   `atomVec` (std::vector<atom>): The primary data store for all atomic information (symbols and coordinates).
-   The `atom` struct's members (`atomSym`, `coord`) are fundamental to storing atomic data.

## Usage Examples

The `Molecule` class is used as a container for atomic geometry that can be manipulated by other classes like `Transform` or `Electrode`.

```cpp
#include "molecule.h"
#include <iostream>

int main() {
    Molecule mol;
    
    // Initialize from an XYZ file
    mol.Initiate("input.xyz", "xyz");
    
    std::cout << "Initial atom count: " << mol.GetAtomCount() << std::endl;
    
    // Add a new Gold atom
    mol.AddAtom("Au", 0.0, 0.0, 5.0);
    
    // Get symbol of the first atom
    if (mol.GetAtomCount() > 0) {
        std::cout << "First atom is: " << mol.GetAtomSym(0) << std::endl;
    }
    
    // Print to a new XYZ file
    mol.PrintInfo("output.xyz", "xyz");
    
    return 0;
}
```

## Dependencies and Interactions

-   **`#include <iostream>`**: Used for basic I/O, likely in `Initiate` for error messages or in `PrintInfo` (though output is primarily to files).
-   **`#include <string>`**: For using `std::string` for atomic symbols and filenames.
-   **`#include <vector>`**: For using `std::vector` to store the list of atoms.
-   **File System**: `Initiate` reads molecule data from files, and `PrintInfo` writes molecule data to files.
-   This class is a core dependency for other classes in `JunctionMod` such as `Transform`, `Electrode`, and `Job`, which operate on or manage `Molecule` objects.
```
