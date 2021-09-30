# Install

## Download

Download the source code of current version of the whole packange from
the Github site, [https://github.com/ryokbys/nap](https://github.com/ryokbys/nap).

---

## Requirements

*pmd* and *fitpot* can be executed in Unix/Linux, macOS X (and Windows) with using the following compiler and library
(list shows tested versions),

- Fortran compiler
    - GNU fortran (9.3.0)
    - Intel Fortran (19.0.4.243)
    - PGI Fortran (19.9)
    - FUJITSU fortran (4.2.1)
- MPI library
    - OpenMPI (4.0.3)
    - Intel MPI (19.0.4.243)


*nappy* utility and *fp.py* program can be used with *Python-3.x.x*, and are dependent on some python packages such as,

- *numpy*
- *scipy*
- *pandas*
- *docopt*
- *ASE*

---


## Build *pmd* and *fitpot*

You can download `nap-master.zip` file from the site. And you can get
`nap-master` directory when you unzip the zip file. For the ease of
following explanation, change the directory name to `nap`.

    $ unzip nap-master.zip
    $ mv nap-master nap
    $ cd nap/

Then you can compile *pmd* and *fitpot* programs as following,

    $ ./configure --prefix=$(pwd)
    $ make pmd fitpot

If you get an error of finding an MPI Fortran compiler when you are
running `configure`, you have to find an MPI Fortran compiler or ask the system administrator and do `configure` again by specifying the path to the MPI Fortran compiler as,

    $ FC=<path/to/fortran-compiler> ./configure --prefix=$(pwd) 

<!-- The option `--prefx=$(pwd)` is not necessary for *pmd* only, but when you use -->
<!-- *fitpot* program, it is required. -->

!!! Note
    If you get an error related to C preprocessor such as,
    
        configure: error: C preprocessor "/lib/cpp" fails sanity check
    
    you may have to specify true C preprocessor path to `configure` command
    by adding an option like `CPP=/usr/bin/cpp`.


Although the *pmd* command should be available with this compilation,
this *pmd* may not be optimized to the system in which it is compiled.
You might need to add some options relevant for the system in which it
is compiled.

### gfortran and openmpi

In the case of `gfortran` with `openmpi`,

    $ ./configure --prefix=$(pwd) FCFLAGS="-O2 -g"

The optimization option over `-O3` seems to cause some errors depending
on the version of gfortran, so it is recommended to use `-O2`
optimization option.

When you are debugging, you may had better set some warning and checking
options enabled as follows.

    $ ./configure --prefix=$(pwd) FCFLAGS="-O2 -g -fbounds-check -Wuninitialized -fbacktrace"

!!! Note
    Compilation with LLVM version gcc is not tested. Use Homebrew version of gcc and openmpi.

!!! Note
    If the compilation stops with the errors "Type mismatch between ..." on MPI routines with using gcc version 10.x.x, you can skip the errors by adding the FCFLAGS option `-fallow-argument-mismatch`. This should not cause any change to pmd results.

### Intel Fortran compiler

If you can use Intel Fortran Compiler`ifort` in your system, the
configure command would be like,

    $ ./configure --prefix=$(pwd) FCFLAGS="-xHOST -O3 -ip -ipo -no-prec-div"

The options `-ip` and `-ipo` have to do with inline expansions and are
relevant to the efficiency of *pmd*.

### PGI fortran compiler

If the MPI fortran command `mpif90` is linked to PGI fortran compiler,
you can use the compiler by just specifying the compiler path as,

    $ FC=/path/to/mpif90 ./configure --prefix=$(pwd) FCFLAGS='-Minfo -O2 -g'



### Fujitsu Fortran in Flow-FX

Since some time ago the compilation had become failed on computation node, so one has to compile on login node using cross-compilers as follows,

    $ ./configure --prefix=$(pwd) FCFLAGS="-Kfast" --host=sparc64
    $ make pmd fitpot
    $ exit

The option `--host=sparc64` is required to specify which architecture is targeted by the execution file.

### Fujitsu Fortran in CX400

In the case of Fujitsu Fortran compiler `mpifrt` in CX400,

    $ ./configure --prefix=$(pwd) FCFLAGS="-Kfast,parallel"


### Helios in Rokkasho-mura

It is Linux OS on Intel CPU, and the compilation seems to be basic one.
But one needs to add specific options as following,

    $ ./configure --prefix=$(pwd) FC=mpiifort FCFLAGS="-xAVX -O3 -ip -ipo -g -CB"

If you don\'t specify the `mpiifor` explicitly, `ifort` is set by
default and the compilation does not work correctly.


### Notes on OpenMP

The OpenMP parallelization become available since v0.10.10 or rev210714. It is enabled if appropriate option is given for `FCFLAGS`, such as `-fopenmp` in the case of gfortran.

The number of threads used by OpenMP must be provided by the environment variable `OMP_NUM_THREADS`, e.g.,

    $ export OMP_NUM_THREADS=4

Note that the parallelization by OpenMP is not very scaled, and currently (2021-07-14) it is recommended to set it about 4 or less than 8. And, if available, use other cores by MPI parallelization.

---

## Setup *nappy* (required for *fp.py*)

To use *nappy* in python program, it is required to add a path to
`nap/nappy` directory to the environment variable `PYTHONPATH`.
You can set this permanently by adding the following line to `~/.bash_profile` in case of *bash* and `~/.zshrc` in case of *zsh*,


    export PYTHONPATH=${PYTHONPATH}:/path/to/nap


You can check whether the path to `nappy` is added to `PYTHONPATH` by
the following command, :

    $ python -c 'import nappy; print(nappy.__file__)'


---

## Tests

There are some examples in `nap/examples/` directory and all the examples contain reference outputs that should be written out when programs are correctly performed.

### Quick test (available since v0.10.1 or rev201130)

You can perform regresssion tests of *pmd* and *fitpot* programs as follows.

    $ cd nap/
    $ make test

The test for *fp.py* can be performed by the following command from `nap/` directory, which could take a fiew minutes since it calls several MD runs in every iteration.

    $ make test-fp

If these tests are fine, they show messages like ` PASS: examples/xxxxx`, otherwise they will show `FAIL: examples/xxxx`.

---

## Examples used for above tests

### Examples for *pmd*

There are three examples for *pmd* program,

- `nap/examples/pmd_W` -- a simple MD using a potential with given parameters.
- `nap/exampels/pmd_BVS_LLZO` -- an MD using screened Coulomb, Morse and angular potentials whose parameters are optimized using *fp.py* program.
- `nap/examples/pmd_DNN_SiO` -- an MD using a neural-network potential whose parameters are optimized using *fitpot* program.

In an example directory, you can run *pmd* program by using *mpirun* command as,

    $ mpirun -np 1 /path/to/pmd

You can test whether the *pmd* program is built correctly or does not degrade after development by comparing its standard output with `out.pmd.REF` file in the directory.

For example, the potential energies that can be extracted from `out.pmd.REF` as,

    $ grep 'Potential energy' out.pmd.REF
    Potential energy=       -463.72014 eV =     -8.587 eV/atom
    Potential energy=       -460.77567 eV =     -8.533 eV/atom

are the potential energies of the system before and after the MD run. These should be identical in the test run.
And also, kinetic energy, puressure and temperature of the system after the MD run are shown within the last 10 lines that can be used for the test.

### An example for *fitpot*

In the `nap/examples/fitpot_DNN_SiO` directory, you can test the *fitpot* program by using *mpirun* command as,

    $ mpirun -np 1 /path/to/fitpot

by comparing its standard output with `out.fitpot.REF`.

For example, RMSEs of energy, force and stress are shown in lines starting with `ENERGY:`, `FORCE:` and `STRESS:`, respectively, whose last values are within the last 10 lines. These values should be identical or resonably close in the test run.

### Examples for *fp.py*

There are two examples for *fp.py* program,

- `nap/examples/fp_LZP` -- fit potential parameters using DF-matching mode (see [fp.py documentation](./fp.md#Reference data for fp.py))
- `nap/examples/fp_LAZP` -- fit potential parameters using any-target mode

In the `nap/examples/fp_LZP` directory, you can test the *fp.py* program as,

    $ python /path/to/nap/nappy/fitpot/fp.py --nproc=4 

by comparing its standard output with `out.fp.REF`.

Since the *fp.py* uses random numbers in the optimization and currently they are not the same in every run of *fp.py*, the resulting numbers are not necessarily identical to `out.fp.REF`. To verify that *fp.py* is performed correctly, check the following,

1. Each line starting with `   iid,Lr,Lth,Lvol,Llat,L=` shows the loss values obtained using the randomly genereated parameters. These values can be different from `out.fp.REF`. But if these values are 100.0000 or NaN, something is wrong.
2. Lines starting with ` step,time,best,vars=` show the current state of optimization process. And the current best value (`6.2877` in `fp_LZP/out.fp.REF`) should be the minimum one among `L` values in the above lines.
