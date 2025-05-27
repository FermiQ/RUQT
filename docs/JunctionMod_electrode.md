# `JunctionMod/electrode.h`

## Overview

This header file, `electrode.h`, defines the `Electrode` class within the `JunctionMod` library. The `Electrode` class is responsible for constructing and attaching different types of electrodes to a central molecular structure (`Molecule` object). It utilizes the `Transform` class for geometric manipulations and the `Molecule` class to represent the atomic structures.

## Key Components

-   **`class Electrode`**:
    -   **Private Members**:
        -   `Transform transformer`: An instance of the `Transform` class, used for geometric operations like alignment.
        -   `void JuncLocate(Molecule& examMole, int& atom1, int& atom2)`: A helper function likely used to find specific junction atoms (e.g., sulfur atoms) in a molecule.
        -   `int NearestH(Molecule& examMole, int ithAtom, char Decision)`: A helper function to find the nearest hydrogen atom to a specified atom. The `Decision` char might control whether to just find ('f') or remove ('r') the hydrogen.
    -   **Public Members (Methods)**:
        -   `void LinearElectrode(Molecule& examMole, int MCount, std::string MType, int* juncAtoms, double BL1, double BL2, bool HydroRep)`: Constructs linear electrodes attached to the molecule.
            -   `examMole`: The molecule to which electrodes are added.
            -   `MCount`: Number of metal atoms in each electrode chain.
            -   `MType`: Atomic symbol of the metal atoms (e.g., "Au").
            -   `juncAtoms`: Pointer to an array of integers, likely containing indices of junction atoms on the molecule and hydrogen atoms to be replaced.
            -   `BL1`: Bond length between the junction atom and the first electrode atom.
            -   `BL2`: Bond length between electrode atoms themselves.
            -   `HydroRep`: Boolean, if true, specified hydrogens are removed.
        -   `void HydroRepElectrode(Molecule& examMole, int MCount, std::string MType, int* juncAtoms, double BL1, double BL2)`: Similar to `LinearElectrode` but specifically designed for cases where hydrogens are replaced, possibly with a different geometry for placing the first electrode atom based on the S-H bond.
        -   `void TipSquareElectrode(Molecule& examMole, std::string MType, int* juncAtoms, double BL1, double BL2)`: Constructs electrodes with a square tip geometry.
        -   `void PyramidElectrode(Molecule& examMole, int layers, std::string MType, int* juncAtoms, double BL1, double BL2)`: Constructs electrodes with a pyramidal (fcc-like) tip geometry.
            -   `layers`: Number of layers in the pyramid.
        -   `void CustomElectrode(Molecule& elecMole, Molecule& juncMole, int* juncEle, double BL)`: Attaches a pre-defined electrode structure (`elecMole`) to a junction molecule (`juncMole`).
            -   `juncEle`: Array of integers specifying junction points and alignment atoms on both molecules.
            -   `BL`: Bond length for attaching the electrode.
        -   `void AddCustomElectrode(Molecule& elecMole, Molecule& juncMole, Molecule& elecMole2, int* juncEle, double BL)`: Attaches two different pre-defined electrode structures (`elecMole`, `elecMole2`) to a junction molecule (`juncMole`).

## Important Variables/Constants

The key "variables" in this header are the parameters passed to the public methods, which define the geometry and composition of the electrodes. These include:
-   `MCount` (int): Number of atoms in an electrode.
-   `MType` (std::string): Element type for electrode atoms (e.g., "Au").
-   `juncAtoms` (int*): Array specifying indices of atoms involved in bonding/alignment.
-   `BL1`, `BL2` (double): Bond lengths for electrode construction.
-   `layers` (int): Number of layers for pyramidal electrodes.
-   `HydroRep` (bool): Flag for hydrogen replacement.

## Usage Examples

An instance of the `Electrode` class would be used by a higher-level component (like the `Job` class) to modify a `Molecule` object by adding electrodes.

```cpp
// In some other file, e.g., job.cpp
#include "molecule.h"
#include "electrode.h"
// ...

Molecule myMolecule;
// ... (load myMolecule with atoms, e.g., from an XYZ file) ...

Electrode electrodeBuilder;
int junction_info[4] = { /* indices of S, H, S, H atoms */ };
double bond_len_S_Au = 2.62;
double bond_len_Au_Au = 2.88;
int num_electrode_atoms = 3;
std::string metal_type = "Au";

// Add linear electrodes, replacing hydrogens
electrodeBuilder.LinearElectrode(myMolecule, num_electrode_atoms, metal_type, junction_info, bond_len_S_Au, bond_len_Au_Au, true);

// myMolecule now has gold atoms added as linear electrodes
// ... (myMolecule.PrintInfo(...) to save or view) ...
```

## Dependencies and Interactions

-   **`#include <iostream>`**: For standard I/O (likely used in the `.cpp` file for debugging or messages).
-   **`#include <string>`**: For using `std::string`.
-   **`"molecule.h"`**: Depends on the `Molecule` class definition for representing and modifying molecular structures. Electrode methods take `Molecule` objects by reference.
-   **`"transform.h"`**: Depends on the `Transform` class definition, as it contains a `Transform` object (`transformer`) to perform geometric operations like aligning molecules before adding electrodes.
-   The methods of this class directly modify the `Molecule` object passed to them by adding new atoms or removing existing ones (hydrogens).
```
