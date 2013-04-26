## Purpose

Numpy offers the save method for easy saving of arrays into .npy and savez for zipping multiple .npy arrays together into a .npz file. cnpy lets you read and write to these formats in C++. The motivation comes from scientific programming where large amounts of data are generated in C++ and analyzed in Python. Writing to .npy has the advantage of using low-level C++ I/O (fread and fwrite) for speed and binary format for size. The .npy file header takes care of specifying the size, shape, and data type of the array, so specifying the format of the data is unnecessary. Loading data written in numpy formats into C++ is equally simple, but requires you to type-cast the loaded data to the type of your choice.

## Installation

Default installation directory is `/usr/local`. To specify a different directory, add `-DCMAKE_INSTALL_PREFIX=/path/to/install/dir` to the cmake invocation in step 4.

    get cmake at www.cmake.org
    create a build directory, say $HOME/build
    cd $HOME/build
    cmake /path/to/cnpy
    make
    make install

## Using

To use, `#include"cnpy.h"` in your source code. Compile the source code mycode.cpp as

    g++ -o mycode mycode.cpp -L/path/to/install/dir -lcnpy

## Description

There are two functions for writing data: `npy_save`, `npz_save`.

There are 3 functions for reading. `npy_load` will load a .npy file. `npz_load(fname)` will load a .npz and return a dictionary of `NpyArray` structues. `npz_load(fname,varname)` will load and return the `NpyArray` for data varname from the specified .npz file.
Note that `NpyArray` allocates `char*` data using `new[]` and *will not* delete the data upon the `NpyArray` destruction. You are responsible for delete the data yourself.
The `order` argument when calling `npy_save` or `npz_save` can take the values "C" or "F", indicating C or Fortran order respectively.

The data structure for loaded data is below. Data is loaded into a a raw byte array. The array shape and word size are read from the npy header. You are responsible for casting/copying the data to its intended data type.

    struct NpyArray {
        char* data;
        std::vector<unsigned int> shape;
        unsigned int word_size;
    };

See example1.cpp for examples of how to use the library. example1 will also be build during cmake installation.
