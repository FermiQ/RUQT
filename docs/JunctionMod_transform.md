# `JunctionMod/transform.h`

## Overview

The `transform.h` header file defines the `Transform` class for the `JunctionMod` library. This class provides a suite of methods for performing various geometric transformations on `Molecule` objects. These transformations include coordinate system conversions (Cartesian to Cylindrical and vice-versa), translations, centering, axis alignment, and more complex operations like bond rotations and device switching simulations.

## Key Components

-   **`class Transform`**:
    -   **Public Members (Methods)**:
        -   `void ConvertCoord(Molecule& examMole, char fromType, char toType)`: Converts the coordinates of all atoms in `examMole` between Cartesian ('C') and Cylindrical ('1' for X-axis, '2' for Y-axis, '3' for Z-axis as cylindrical axis) coordinate systems.
        -   `void CoordEdit(Molecule& examMole, double xTrans, double yTrans, double zTrans)`: Translates all atoms in `examMole` by the given `xTrans`, `yTrans`, and `zTrans` values. This method can also be used to edit cylindrical coordinates if the molecule is already in a cylindrical representation (e.g., translating the cylindrical axis component or radius/angle).
        -   `void SelectiveEdit(Molecule& examMole, double xTrans, double yTrans, double zTrans, std::vector<int>& atomSel)`: Applies a translation (or cylindrical coordinate modification) only to a selected subset of atoms specified by their indices in `atomSel`. The current implementation in the `.cpp` file shown only modifies the 'z' coordinate.
        -   `void Center(Molecule& examMole, int A1)`: Translates the entire molecule so that atom `A1` is moved to the origin (0,0,0).
        -   `void AlignAxis(Molecule& examMole, char axis, int A1, int A2)`: Aligns the molecule such that atom `A1` is at the origin, and the vector from `A1` to `A2` is aligned with the specified Cartesian `axis` ('x', 'y', or 'z'). This involves centering, conversions to cylindrical coordinates, rotations (by modifying angles in cylindrical coordinates), and conversion back to Cartesian.
        -   `void BondRotate(Molecule& examMole, double theta, int atom1, int atom2, std::vector<int>& atomSel)`: Rotates a group of selected atoms (`atomSel`) around the bond defined by `atom1` and `atom2` by an angle `theta`. It internally uses `AlignAxis` to align the bond with the x-axis, converts to X-cylindrical coordinates, applies the rotation to the selected atoms' angles, and then converts back to Cartesian.
        -   `void DeviceSwitch_ODSP(Molecule& examMole, int* axiAtoms, double angleDisp, double angleInc, std::vector<int>& atomSelRot, std::vector<int>& atomSelRec, std::string filename)`:
            -   "ODSP" stands for "Oddly Specific Types of Transformations."
            -   This method performs a series of bond rotations to simulate a molecular switch or device. It involves two separate rotations: one group of atoms (`atomSelRot`) is rotated around an axis defined by `axiAtoms[0]`-`axiAtoms[1]`, and another group (`atomSelRec`) is rotated (often in the opposite direction) around an axis defined by `axiAtoms[2]`-`axiAtoms[3]`.
            -   `angleDisp`: Total angular displacement.
            -   `angleInc`: Angular increment for each step.
            -   The method iterates, applying incremental rotations and saving the molecule's geometry at each step to a file named `filename_counter.gamess`.

## Important Variables/Constants

The methods in this class primarily operate on `Molecule` objects and use parameters to define the transformation specifics:
-   `fromType`, `toType` (char): Specify coordinate systems for conversion.
-   `xTrans`, `yTrans`, `zTrans` (double): Translation distances.
-   `atomSel` (std::vector<int>&): Vector of atom indices for selective operations.
-   `A1`, `A2` (int): Atom indices for defining centers or axes.
-   `axis` (char): Specifies the Cartesian axis for alignment.
-   `theta` (double): Rotation angle.
-   `axiAtoms` (int*): Array of atom indices for defining rotation axes in `DeviceSwitch_ODSP`.
-   `angleDisp`, `angleInc` (double): Angular displacement and increment for `DeviceSwitch_ODSP`.
-   `PI` (const double): Mathematical constant Pi (3.14159...) is used internally in `AlignAxis` (defined in the `.cpp` file).

## Usage Examples

An instance of `Transform` would be used by higher-level components (like the `Job` class) to modify `Molecule` objects.

```cpp
#include "molecule.h"
#include "transform.h"
#include <vector>

int main() {
    Molecule mol;
    mol.Initiate("some_molecule.xyz", "xyz"); // Assume this loads a molecule

    Transform transformer;

    // Center the molecule on its first atom
    if (mol.GetAtomCount() > 0) {
        transformer.Center(mol, 0);
    }

    // Align the bond between atom 0 and 1 with the Z-axis
    if (mol.GetAtomCount() > 1) {
        transformer.AlignAxis(mol, 'z', 0, 1);
    }
    
    // Rotate a subset of atoms (e.g., atoms 2, 3, 4) around the bond 0-1 by 90 degrees (PI/2)
    if (mol.GetAtomCount() > 4) {
        std::vector<int> atoms_to_rotate;
        atoms_to_rotate.push_back(2);
        atoms_to_rotate.push_back(3);
        atoms_to_rotate.push_back(4);
        transformer.BondRotate(mol, 1.570796, 0, 1, atoms_to_rotate);
    }

    mol.PrintInfo("transformed_molecule.xyz", "xyz");

    return 0;
}
```

## Dependencies and Interactions

-   **`#include <vector>`**: For using `std::vector`, particularly for `atomSel` in `SelectiveEdit` and `BondRotate`, and `atomSelRot`/`atomSelRec` in `DeviceSwitch_ODSP`.
-   **`#include <string>`**: For `std::string` used in `DeviceSwitch_ODSP` for filenames.
-   **`"molecule.h"`**: Critically depends on the `Molecule` class definition, as all transformation methods operate on `Molecule` objects passed by reference.
-   The methods directly modify the coordinates of atoms within the `Molecule` object.
-   The `DeviceSwitch_ODSP` method also interacts with the file system by saving multiple molecule geometries.
```
