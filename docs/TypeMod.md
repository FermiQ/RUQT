# `TypeMod.f90`

## Overview

The `TypeMod.f90` Fortran module defines several custom data types. These types are used to structure and manage complex data, particularly related to electron correlation amplitudes (like B1 and B2 for Configuration Interaction or Coupled Cluster methods) and Green's functions, which are central to the quantum chemistry and transport calculations performed in other parts of the project.

## Key Components

This module primarily consists of type definitions:

-   **`type :: inner`**:
    -   Contains a single component: `n`, a 2D allocatable array of `real(8)`. This likely represents a matrix of values, possibly indexed by orbital pairs.

-   **`type :: outer`**:
    -   Contains a single component: `m`, a 2D allocatable array where each element is of type `inner`. This creates a nested structure, effectively a 4D array (or a matrix of matrices), suitable for storing two-electron integrals or amplitudes indexed by four orbitals (e.g., `<ij|ab>`).

-   **`type :: B2`**: Represents data for two-particle (double excitation) amplitudes.
    -   `aa`: Of type `outer`. Likely stores alpha-alpha spin components of T2 amplitudes or similar quantities.
    -   `ab`: Of type `outer`. Likely stores alpha-beta spin components.
    -   `bb`: Of type `outer`. Likely stores beta-beta spin components.
    -   `nospin`: Of type `outer`. Potentially for spin-integrated or spin-free quantities.

-   **`type :: virtorb`**: Represents data related to virtual orbitals, likely for one-particle (single excitation) amplitudes.
    -   `o`: A 2D allocatable array of `real(8)`. This could store T1 amplitudes, indexed by an occupied and a virtual orbital (e.g., `t_i^a`).

-   **`type :: B1`**: Represents data for one-particle (single excitation) amplitudes.
    -   `a`: Of type `virtorb`. Likely stores alpha-spin T1 amplitudes.
    -   `b`: Of type `virtorb`. Likely stores beta-spin T1 amplitudes.

-   **`type :: ingreen`**: Represents a Green's function matrix at a specific energy.
    -   `gf`: A 2D allocatable array of `real(8)`. Stores the Green's function matrix elements.

-   **`type :: energr`**: Represents an energy-dependent Green's function.
    -   `en`: A 1D allocatable array where each element is of type `ingreen`. This allows storing multiple Green's function matrices, each corresponding to a different energy point.

-   **Global Variables**:
    -   `integer, allocatable, dimension(:) :: Virt_Index, Occ_Index`: These appear to be module-level allocatable arrays intended to store indices for virtual and occupied orbitals, respectively. Their usage would depend on other parts of the code that `USE TypeMod`.

## Important Variables/Constants

The "variables" in this module are the components within the defined types. The structure of these types (nested arrays) is the most important aspect. For example:
-   `B2data%aa%m(i,j)%n(k,l)` would access a specific `real(8)` value from a `B2` type variable, representing a quantity indexed by four indices `i,j,k,l`.
-   `B1data%a%o(p,q)` would access a specific `real(8)` value from a `B1` type variable, indexed by `p,q`.
-   `G_S%en(energy_index)%gf(orb1, orb2)` would access an element of a Green's function matrix for a given `energy_index`.

## Usage Examples

These types are intended to be used in other Fortran routines by `USE`ing `TypeMod`.

```fortran
MODULE CalculationRoutine
  USE TypeMod
  IMPLICIT NONE

  TYPE(B1) :: t1_amplitudes
  TYPE(B2) :: t2_amplitudes
  TYPE(energr) :: system_greens_function
  INTEGER :: num_occupied, num_virtual, num_energy_points, num_orbitals
  INTEGER :: i, j, a, b, e_idx

  ! ... (initialize num_occupied, num_virtual, etc.) ...

  ! Allocate B1 amplitudes
  ALLOCATE(t1_amplitudes%a%o(1:num_virtual, 1:num_occupied))
  ALLOCATE(t1_amplitudes%b%o(1:num_virtual, 1:num_occupied))
  t1_amplitudes%a%o = 0.0 ! Initialize

  ! Allocate B2 amplitudes (simplified example for one component)
  ALLOCATE(t2_amplitudes%ab%m(1:num_virtual, 1:num_occupied))
  DO i = 1, num_virtual
    DO j = 1, num_occupied
      ALLOCATE(t2_amplitudes%ab%m(i,j)%n(1:num_virtual, 1:num_occupied))
      t2_amplitudes%ab%m(i,j)%n = 0.0 ! Initialize
    END DO
  END DO

  ! Allocate Green's function storage
  ALLOCATE(system_greens_function%en(1:num_energy_points))
  DO e_idx = 1, num_energy_points
    ALLOCATE(system_greens_function%en(e_idx)%gf(1:num_orbitals, 1:num_orbitals))
    system_greens_function%en(e_idx)%gf = 0.0 ! Initialize
  END DO

  ! ... (populate and use these typed variables) ...

END MODULE CalculationRoutine
```

## Dependencies and Interactions

-   This module is foundational and is likely `USE`d by most other Fortran files in the project that deal with advanced electronic structure data (e.g., `Build_B0_CISD.f90`, `Build_G_SD_Invert.f90`, `RUQT.f90`).
-   It has no direct dependencies on other custom modules, only on intrinsic Fortran types and allocatable array features.
```
