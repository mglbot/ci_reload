
# code
- generalize text file readers to work with any tensor
- single type for any-rank tensor
    - template on dimension
    - type (float, double, mixed/iterative)
    - storage layout (row/column),
    - storage backend (raw array, std::vector, kokkos)
    - batching layout (matrix contiguous, interleaved)
- remove all std::vector<>
    - possibly requires supporting iterators for std::transform and other 
      std:: algorithms
    - quadrature to use tensors.h
- support resizing for fk::tensors
- non-blas option for tensor classes
    - configure through cmake
    - disable functionality for now (using `assert`+explanatory comments; add
      back (without blas) as needed
- tensor comparisons that color the incorrect values (disable for NDEBUG)
- compile-time switch between row/column major ordering
    - template paremeter along with other storage options
- asserts for tensors ctors?
- const types where possible
- `size_t` instead of ints?
- noexcept for optimization opportunities?
- remove file dependencies in operator twoscale
- remove file dependencies in tests for initial conditions 1D
- make all sizes `size_t` type instead of int/unsigned int
- package up parameters, function inputs, etc. into sensible types

# cmake
- restructure cmakelists files into subdirectories
- configure option for handling blas
    - no blas
    - use my own or system blas

# testing
- coverage reports
- performance testing
- `initial_conditions_1D()` test input is read from bin matlab files, not generated


//----------------------------------------------------------------------------
//
// general:
//  Solve the Vlasov-Poisson system of equations. See
//  DG-SparseGrid/Vlasov-Poisson-version2/DG-SG-Vlasov-Poisson-version2.pdf
//
// some conventions
//  for right now, I'm keeping the (sometimes non-optimal) variable names from
//  the reference matlab code, until we can move away from it completely
//
// some notes: roughly, we do something like
// - set up initial conditions for a PDE, and the analytic functions to evaluate
//   arbitrary points (these live in PDE/Vlasov[1-6].m
// - discretize the function by evaluating on the 'configuration space' grid
// - do a forward wavelet transform to 'wavelet space'
// - in wavelet space:
//    - the number of 'levels' is the number of subdivisions (by 2) of an
//      interval: e.g. level 1: | \ / |, level 2: |\/\/| level 3: |VVVV|
//    - the number of 'levels' is the point at which you truncate the sum (e.g.
//      the wavenumber if it were a fourier transform); the number of
//      coefficients (terms) kept from the infinite expansion
//    - the 'degree' is the number of coefficients (terms) of the polynomial
//      used to represent the functions at each level (in Lin's convention, it
//      is the actual polynomial degree-1).
//    - either/both the number of levels and polynomial degree could be
//      increased to improve accuracy/convergence. Usually the polynomial degree
//      helps more than adding levels.
//
//----------------------------------------------------------------------------

