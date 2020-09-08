# Cholmod and Scikit-Sparse for Windows

[![996.icu](https://img.shields.io/badge/link-996.icu-red.svg)](https://996.icu)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![LICENSE](https://img.shields.io/badge/license-Anti%20996-blue.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE)


[Cholmod](http://www.cise.ufl.edu/research/sparse/SuiteSparse/) is a powerful package for sparse matrix calculation and [Scikit-Sparse](https://pypi.org/project/scikit-sparse) is a python interface. It is convenient to set up the Cholmod and Scikit-Sparse (CSC) environment in Linux and Mac OS, but it may be troublesome in Windows. Thanks to jlblancoc's package [suitesparse-metis-for-windows](https://github.com/jlblancoc/suitesparse-metis-for-windows), I make a modification for the python interface. Here are the instructions.

## 1. Overview

It takes two steps to set up the CSC environment.
1. Compile the `*.h`, `*.lib` and `*.dll` files of Cholmod. It can be included and linked for C/C++.
2. Compile the python package Scikit-Sparse.

This repository has been tested on

- Windows 10 64bit
- Anaconda 3
- Microsoft Visual C++ 17

**This repository is not applicable to Python 2 (Anaconda 2) on Windows 10**. Because in the Step II, compiling C codes for Python 2 requires VC 9.0 (VS 2008), which can not be installed on Windows 10. The compiler used in the Step I is supposed to be the same as the one used in the Step II.

Because metis package has many errors when compiling, I removed the metis package and modified the `CSC_ROOT/CMakeLists.txt`.

## 2. Instructions

### 2.1 Preparation

1. Microsoft Visual Studio 2017 including VC 14 (VS 2015 may be Ok). C++ compiler tools are required. You can add them by the Stack Overflow [post](https://stackoverflow.com/a/47443816/11805488).
2. Anaconda 3.6 64bit including numpy and scipy.
3. Download [CMake](https://cmake.org/).
4. Clone or Download this repository, say `CSC_ROOT`. If you require the latest version, just download them and merge them with the target folders.
   - suitesparse-metis-for-windows version: 1.3.1
   - scikit-sparse version: 0.4.4. If you replace it by a new package, don't forget to **modify the `setup.py` to link the headers and libraries.**
   - SuiteSparse version: 5.3.0. It is under the `suitesparse-metis-for-windows-1.3.1/`

### 2.1 Compile Cholmod

1. Run CMake, then:
   1. Set the "Source code" folder to `CSC_ROOT/suitesparse-metis-for-windows-1.3.1`.
   2. Set the "Build the binaries" folder to `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build`.
   3. Click "Configure" and select "Visual Studio 15 2017 Win64" (for 64bit OS). Tips: Make sure to build in x64 mode. "HAVE_COMPLEX" is not recommended.
   4. Click "Generate".
   5. Click "Open Project" to open the "SuiteSparseProject" solution.
2. In Visual Studio, build the "INSTALL" project in "Release" mode. It may show hundreds of warnings, but it's OK. (Note: according to this [issue#2](https://github.com/xmlyqing00/Cholmod-Scikit-Sparse-Windows/issues/2#issuecomment-589835095) report, **select "Release" mode is necessary** in case of missing `cholmod.lib`.)
3. `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install` contains the generated libraries. 
   - `*.h` header files are in `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/include/suitesparse`
   - `*.lib` libraries are in `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/lib64` and `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/lib64/lapack_blas_windows`
   - `*.dll` dynamic libraries are in `CSC_ROOT/suitesparse-metis-for-windows-1.3.1/build/install/lib64/lapack_blas_windows`.
4. Now, the Cholmod package has been set up for C/C++.

### 2.2 Compile Scikit-Sparse

1. Open "PowerShell" and type `cd scikit-sparse-0.4.4`.
2. Type `python setup.py build` to compile the package. The `setup.py` has been modified to link the headers and libraries.
3. Type `python setup.py install` to copy the executable code into the Anaconda folders.

### 2.3 Testing

Congratulations! Everything is done! In python command. Type

```
from sksparse.cholmod import cholesky
```

If nothing happens, the installation is done! For detailed usage and test, please refer to `scikit-sparse-0.4.4/sksparse/test_cholmod.py`.


## 3 Update Logs
Give me a star if you like this repository.
Feel free to open an issue or start an pull request.

- Thanks to the [Issue 4](https://github.com/xmlyqing00/Cholmod-Scikit-Sparse-Windows/issues/4), the scikit-sparse is upgraded from 0.4.3 to 0.4.4. Cython is not included in scikit-sparse 0.4.4.
- Cython is added by [Pull 3](https://github.com/xmlyqing00/Cholmod-Scikit-Sparse-Windows/pull/3).
