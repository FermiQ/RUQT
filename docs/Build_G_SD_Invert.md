# `Build_G_SD_Invert.f90`

## Overview

The Fortran subroutine `Build_G_SD_Invert` appears to be a core component of a quantum chemistry program, likely involved in calculating Green's functions (`G_C`) for a molecular system, potentially including electron correlation effects via Singles and Doubles (SD) excitations. It handles reading molecular orbital (MO) energies and coefficients, T1 and T2 amplitudes (from CISD or similar methods), and then constructs and inverts a Green's function matrix. It seems to support interfaces with quantum chemistry software like GAMESS and Maple.

## Key Components

- **`subroutine Build_G_SD_Invert(...)`**: The main subroutine.
    - **Arguments (selected)**:
        - `G_C`: Output complex matrix, likely the central block of the Green's function.
        - `Sigma_l`, `Sigma_r`: Input complex matrices, likely self-energies for left and right contacts/electrodes.
        - `energy`: Input real value, the energy at which the Green's function is calculated.
        - `size_l`, `size_c`, `size_lc`, `size_lcr`, `norb`, `numocc`, `numvirt`, `numfcore`, `numfvirt`, `numatomic`: Integers defining sizes of different orbital subspaces (left, central, active, core, virtual, etc.).
        - `inputfile`: Character string, base name for input files.
        - `iter`: Integer, iteration number (controls initialization).
        - `B1data`, `B2data`: Input data of custom types `B1` and `B2` (likely containing T1 and T2 amplitudes).
        - `mo_ener`, `mo_coeff`, `mo_coeff2`: Real arrays for MO energies and coefficients.
        - `doubles`: Logical flag, if `.true.`, considers double excitations (and T2 amplitudes).
        - `currentflag`: Logical flag, controls part of the calculation flow.
        - `G_S`: Output data of custom type `energr` (likely stores energy-dependent Green's function).
        - `corr_ener`: Real value, correlation energy.
        - `use_b0`: Logical flag, if `.true.`, uses B0 coefficient.
        - `gamess`, `maple`: Logical flags, indicate source of T2/MO data.
        - `b0_type`: Character string, type of B0 calculation (e.g., "cisd").
    - **Logic**:
        1.  **Initialization (if `iter.eq.1`)**:
            *   Allocates and initializes `B1data`, `B2data` structures if `doubles` is true.
            *   Allocates `mo_ener`, `mo_coeff`, `mo_coeff2`.
            *   If `doubles` is true, reads T1 and T2 amplitudes from files (e.g., `inputfile.T2`).
                *   Handles different formats for GAMESS and Maple.
            *   If `doubles` and `use_b0` and `b0_type.eq."cisd"` are true, calls `Build_B0_CISD` to get `cisd_b0`.
            *   Reads MO coefficients and energies from files (e.g., `inputfile.mo_dat` for GAMESS, `inputfile.scf_dat` for Maple).
            *   Allocates `G_S` structure.
        2.  **Green's Function Construction (if `currentflag.eqv..false.`)**:
            *   Allocates `temp_gf` (temporary Green's function matrix in MO basis).
            *   Initializes diagonal elements of `temp_gf` based on `energy` and `mo_ener`.
                *   If `doubles` is true, includes `corr_ener` and potentially `cisd_b0` or `b0_coeff` scaling.
                *   Handles frozen core (`numfcore`) and frozen virtual (`numfvirt`) orbitals separately.
            *   If `doubles` is true (and not `singles`), adds further terms to `temp_gf` using `B1data` and `B2data` to account for electron correlation. This involves complex summations over different orbital indices.
            *   Transforms `temp_gf` from MO basis to AO (atomic orbital) basis using `mo_coeff` and `mo_coeff2` (via `matmul_dgemm2`), stores it in `G_S%en(ener_val)%gf`, and then inverts it (via `inv_real`).
        3.  **Final `G_C` Calculation**:
            *   Extracts the central part of the AO Green's function `G_S%en(ener_val)%gf` into `G_C`.
            *   Subtracts self-energies `Sigma_l` and `Sigma_r`.
            *   Inverts the resulting `G_C` matrix (via `inv`).

## Important Variables/Constants

- **`G_C` (complex(8), allocatable, dimension(:,:))**: The primary output, the central block of the Green's function.
- **`B1data`, `B2data` (type B1, B2)**: Structures holding single and double excitation amplitudes.
- **`mo_ener` (real(8), allocatable, dimension(:))**: Molecular orbital energies.
- **`mo_coeff`, `mo_coeff2` (real(8), allocatable, dimension(:,:))**: Molecular orbital coefficients.
- **`temp_gf` (real(8), allocatable, dimension(:,:))**: Temporary Green's function matrix in MO basis.
- **`cisd_b0` (real(8))**: B0 coefficient for CISD, calculated if `use_b0` and `b0_type` are set.
- **`corr_ener` (real(8))**: Correlation energy.
- **`energy` (real(8))**: Energy point for Green's function calculation.
- **`inputfile` (character(len=40))**: Base name for reading various data files.
- **`gamess`, `maple` (logical)**: Flags to control file reading logic for different quantum chemistry packages.
- **`doubles`, `singles` (logical)**: Flags to control the level of theory (inclusion of double/single excitations).
- **`27.211396132`**: Conversion factor, likely Hartree to eV.

## Usage Examples

This subroutine is an internal part of a larger calculation. It would be called after initial setup (like reading input geometry, running a Hartree-Fock calculation, and potentially a post-HF calculation like CISD to get T-amplitudes).

```fortran
! Assuming all input parameters and data structures are properly initialized
call Build_G_SD_Invert(G_C_matrix, Sigma_left, Sigma_right, current_energy, &
     size_l_val, size_c_val, size_lc_val, size_lcr_val, num_orbitals, &
     "my_calc", num_occupied, num_virtual, iter_num, B1_amplitudes, B2_amplitudes, &
     orbital_energies, orbital_coeffs, orbital_coeffs_transposed, &
     use_doubles_flag, current_calculation_flag, total_energy_points, current_energy_index, &
     Greens_function_store, correlation_energy_val, num_atomic_orbitals, B0_coefficients_array, &
     use_b0_flag, is_gamess_input, is_maple_input, num_frozen_core, num_frozen_virtual, "cisd")
! G_C_matrix will now contain the calculated Green's function for the central region
```

## Dependencies and Interactions

- **`InterfaceMod2`**: Used, likely defines `Build_B0_CISD`.
- **`TypeMod`**: Used, defines custom data types like `B1`, `B2`, and `energr`.
- **`FunctionMod`**: Used, provides matrix functions like `matmul_dgemm2`, `inv`, and `inv_real`.
- **File System**: Reads data from several files derived from `inputfile` (e.g., `*.T2`, `*.mo_dat`, `*.scf_dat`).
- **`Build_B0_CISD`**: Called if `use_b0` and `b0_type == "cisd"`.
- This subroutine is central to calculating transport properties or spectral functions, as Green's functions are key to these.
```
