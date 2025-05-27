# `InterfaceMod.f90`

## Overview

This Fortran file defines several modules (`InterfaceMod`, `InterfaceMod2`, `FunctionMod`) that declare interfaces for various subroutines and functions used within the quantum chemistry application. These interfaces ensure that procedures are called with correct argument types and facilitate modular programming. The procedures cover tasks like setting calculation flags, reading Hartree-Fock data from different quantum chemistry packages (QChem, Molcas, GAMESS, PySCF), calculating electrode couplings, partitioning matrices, reading input parameters, and performing matrix operations.

## Key Components

### `Module InterfaceMod`
This module contains interfaces for a wide range of subroutines:

-   **`subroutine Flag_set(...)`**: Sets logical flags based on input strings to control calculation type (CISD, RDM, HF, DFT) and quantum chemistry package used (QChem, GAMESS, PySCF, Maple, Molcas).
-   **`subroutine Get_HF_QChem(...)`**: Interface for a subroutine to read Hartree-Fock data (two-electron integrals `H_two`, overlap matrix `Smat`) from QChem output.
-   **`subroutine Get_HF_Molcas(...)`**: Interface for reading HF data from Molcas output.
-   **`subroutine Get_HF_Molcas2(...)`**: Another interface for reading HF data from a specific Molcas data file.
-   **`subroutine Get_HF_GAMESS(...)`**: Interface for reading HF data from GAMESS output.
-   **`subroutine Get_HF_PySCF(...)`**: Interface for reading HF data from PySCF output.
-   **`subroutine Get_HF_libint(...)`**: Interface for reading HF data using `libint` (one-electron integrals `H_one`, overlap `Smat`, MO coefficients `mo_coeff`, etc.).
-   **`subroutine Calculate_Coupling_MoleculeWBL(...)`**: Interface for calculating coupling strengths (`Coupling_R`, `Coupling_L`) in a Wide Band Limit (WBL) for a molecule.
-   **`subroutine Electrodes_MoleculeWBL(...)`**: Interface for calculating self-energies (`Sigma_l`, `Sigma_r`) for molecular electrodes in WBL.
-   **`subroutine PartitionHS_MoleculeWBL(...)`**: Interface for partitioning overlap (`Smat`) and Hamiltonian matrices for molecular WBL calculations.
-   **`subroutine Electrodes_MetalWBL(...)`**: Interface for calculating self-energies for metallic electrodes in WBL.
-   **`subroutine PartitionHS_MetalWBL(...)`**: Interface for partitioning overlap and Hamiltonian matrices for metallic WBL calculations.
-   **`subroutine ReadInput(...)`**: Interface for a comprehensive input reading subroutine, setting up various calculation parameters (orbital numbers, energy ranges, voltages, file names, theory flags, etc.).
-   **`subroutine Build_G_SD_Invert(...)`**: Interface for the Green's function construction subroutine (detailed in `Build_G_SD_Invert.md`).

### `Module InterfaceMod2`
This module contains an interface for:

-   **`subroutine Build_B0_CISD(...)`**: Interface for the B0 coefficient calculation subroutine (detailed in `Build_B0_CISD.md`).

### `Module FunctionMod`
This module contains interfaces for mathematical utility functions:

-   **`function matmul_dgemm(leftmatrix,rightmatrix)`**: Interface for a double-precision general matrix multiplication.
-   **`function matmul_zgemm(leftmatrix,rightmatrix)`**: Interface for a complex double-precision general matrix multiplication.
-   **`subroutine matmul_dgemm2(leftmatrix,rightmatrix,matsol)`**: Interface for a subroutine performing double-precision matrix multiplication, storing result in `matsol`.
-   **`function inv(A)`**: Interface for inverting a complex matrix.
-   **`function inv_real(A)`**: Interface for inverting a real matrix.
-   **`function adjoint(A,norb)`**: Interface for calculating the adjoint of a complex matrix.
-   **`function fermi_function(energy,fermi_energy,KT)`**: Interface for calculating the Fermi-Dirac distribution value.
-   **`function FirstIndex(i,k)`**: Interface for a function that likely computes a packed 1D index from two 2D indices (e.g., for symmetric matrices).
-   **`function CompositeIndex(ik,jl)`**: Interface for a function that likely computes a packed 1D index from two compound indices (e.g., for two-electron integrals).

## Important Variables/Constants

This file primarily defines interfaces, so the "variables/constants" are the argument lists of these procedures. Key argument types recurring across interfaces include:
-   `real(8)`: Double-precision floating-point numbers for energies, matrix elements, etc.
-   `complex(8)`: Double-precision complex numbers for Green's functions, self-energies.
-   `integer`: For sizes, counts, flags.
-   `character(len=...)`: For filenames and string identifiers.
-   `logical`: For flags controlling program flow.
-   Custom types like `B1`, `B2`, `energr` (presumably defined in `TypeMod`).

## Usage Examples

These modules are not used directly but are `USE`d by other Fortran files in the project. For example, a calculation routine might `USE InterfaceMod` to ensure it correctly calls `ReadInput` or `Get_HF_GAMESS`.

```fortran
PROGRAM MainCalculation
  USE InterfaceMod
  USE TypeMod ! Assuming types B1, B2 etc. are here
  IMPLICIT NONE

  ! Declare variables matching the interface definitions
  character(len=100) :: inputFile_name
  integer :: num_orbitals, num_occupied
  ! ... other variables ...
  real(8), allocatable, dimension(:,:) :: H_matrix, S_matrix
  
  ! Call a subroutine whose interface is defined in InterfaceMod
  call ReadInput(inputFile_name, num_orbitals, ...) 
  call Get_HF_GAMESS(inputFile_name, num_orbitals, H_matrix, S_matrix, num_orbitals)
  ! ... further calculations ...
END PROGRAM MainCalculation
```

## Dependencies and Interactions

- **`TypeMod`**: Several interfaces (e.g., `Build_G_SD_Invert`, `Build_B0_CISD`) use custom types (`B1`, `B2`, `energr`) which are expected to be defined in `TypeMod`.
- The interfaces defined here are crucial for the interaction between different parts of the Fortran codebase, ensuring that subroutines and functions are called correctly.
- The `Get_HF_*` series of interfaces suggest interaction with external quantum chemistry software file formats.
```
