# Cholmod and Scikit-Sparse for Windows

[Cholmod](http://www.cise.ufl.edu/research/sparse/SuiteSparse/) is a powerful package for sparse matrix calculation and [Scikit-Sparse](https://pypi.org/project/scikit-sparse) is a python interface. It is convenient to set up the Cholmod and Scikit-Sparse (CSC) environment in Linux and Mac OS, but it may be troublesome in Windows. Thanks to jlblancoc's package [suitesparse-metis-for-windows](https://github.com/jlblancoc/suitesparse-metis-for-windows), I make an improvement for python interface. Here are the instructions.

## 1. Overview

It takes two steps to set up the CSC environment.

   	1. Compile the `*.h`, `*.lib` and `*.dll` files of Cholmod. It can be included and linked for C/C++.
   	2. Compile the python package Scikit-Sparse.

This repository has been tested on Windows 10 64bit.

Because metis package has many errors when compiling, I removed the metis package and modified the `CSC_ROOT/CMakeLists.txt`

## 2. Instructions

### 2.1 Preparation

1. Microsoft Visual Studio 2017 including VC 14 (VS 2015 may be Ok).
2. Anaconda 3.6 64bit including numpy and scipy.
3. Download [CMake](https://cmake.org/).
4. Clone or Download this repository, say `CSC_ROOT`. If you require the latest version, just download them and merge them with the target folders.
   - suitesparse-metis-for-windows version: 1.3.1
   - scikit-sparse version: 0.4.3
   - SuiteSparse version: 5.3.0 

### 2.1 Compile Cholmod

1. Run CMake, then:
   1. Set the "Source code" folder to `CSC_ROOT/suitesparse-metis-for-windows-1.3.1`.
   2. Set the "Build the binaries" folder to `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build`.
   3. Click "Configure" and select "Visual Studio 15 2017 Win64" (for 64bit OS). Tips: "HAVE_COMPLEX" is not recommended.
   4. Click "Generate".
   5. Click "Open Project" to open the "SuiteSparseProject" solution.
2. In Visual Studio, build the "INSTALL" project in "Release" mode. It may show hundreds of warnings, but it's OK.
3. `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install` contains the generated libraries. 
   - `*.h` header files are in `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/include/suitesparse`
   - `*.lib` libraries are in `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/lib64` and `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/lib64/lapack_blas_windows`
   - `*.dll` dynamic libraries are in `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/lib64/lapack_blas_windows`. It should be added into system path (Important !!).
4. Now, the Cholmod package has been set up for C/C++.

### 2.2 Compile Scikit-Sparse

1. Open "PowerShell" and type `cd scikit-sparse-0.4.3`.
2. Type `python setup.py build` to compile the package. The `setup.py` has been modified to link the headers and libraries.
3. Type `python setup.py install` to copy the executable code into the Anaconda folders.

### 2.3 Testing

Congratulations! Everything is done! In python command. Type

```
from sksparse.cholmod import cholesky
```

If nothing happens, it works! 