# FortranFiles.jl

A Julia package for reading/writing Fortran unformatted (i.e. binary) files.

[![Documentation](https://img.shields.io/badge/docs-latest-blue.svg)](https://traktofon.github.io/FortranFiles.jl/latest/)
[![Build Status](https://travis-ci.org/traktofon/FortranFiles.jl.svg?branch=for07)](https://travis-ci.org/traktofon/FortranFiles.jl)
[![coveralls Status](https://coveralls.io/repos/traktofon/FortranFiles.jl/badge.svg?branch=for07&service=github)](https://coveralls.io/github/traktofon/FortranFiles.jl?branch=for07)
[![codecov.io Status](http://codecov.io/github/traktofon/FortranFiles.jl/coverage.svg?branch=for07)](https://codecov.io/gh/traktofon/FortranFiles.jl/branch/for07)

Supported Julia versions (in the `for07` branch):
- 0.7: working fine
- 0.6 and earlier: not supported

## Quickstart ##

Full documentation is available at <https://traktofon.github.io/FortranFiles.jl/latest/>.

Installation:
```
# type ] to enter package managing mode
pkg> add FortranFiles#for07
```

Example usage for reading files:
```julia
using FortranFiles

# opening a file for reading
f = FortranFile("data.bin")

# reading a single scalar from the file
# (if there is more data in the record, it will be skipped -- this is Fortran behavior)
x = read(f, Float64)

# reading a 1D array (here of length 10)
vector = read(f, (Float64,10))

# reading into an already allocated array
vector = zeros(10)
read(f, vector)

# reading a 2D array -- alternative syntaxes
matrix = read(f, (Float64,10,10))
matrix = read(f, (Float64,(10,10)))

# reading a CHARACTER*20 string
fstr = read(f, FString{20})
# convert this string to a Julia String, discarding trailing spaces
jstr = trimstring(fstr)

# reading a record with multiple data
i, strings, zmatrix = read(f, Int32, (FString{20},10), (ComplexF64,10,10))

# macro for reading a record where the size is not known ahead
@fread f n::Int32 vector::Array{Float64}(undef, n)

# skipping over a record
read(f)

# go back to the beginning of the file
rewind(f)
```

Example usage for writing files:
```julia
# opening a file for writing
f = FortranFile("data.bin", "w")

# take care when defining the Julia data to be written into the file,
# noting the correspondence between Julia and Fortran datatypes
i = Int32(1)                 # INTEGER(KIND=4)
x = 1.0                      # REAL(KIND=REAL64), usually the same as DOUBLE PRECISION
A = zeros(Float32, 10, 10)   # REAL,DIMENSION(10,10)
s = FString(20, "blabla")    # CHARACTER(LEN=20)

# write all these data into a single record
write(f, i, x, A, s)

# close the file
close(f)
```

