# Install

## Download

Download the source code of current version of the whole packange from
the Github site, [https://github.com/ryokbys/nap](https://github.com/ryokbys/nap).

---

## Requirements

*pmd* and *fitpot* can be executed in Unix/Linux, macOS X, and Windows with using a
Fortran compiler and an MPI library (*mpif90*).

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
following explanation, change the directory name to `nap` as,

    $ unzip nap-master.zip
    $ mv nap-master nap
    $ cd nap/

Then you can compile *pmd* and *fitpot* programs as following,

    $ ./configure --prefix=$(pwd)
    $ cd pmd
    $ make pmd
    $ cd ../fitpot
    $ make fitpot

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



### Fujitsu Fortran in FX?

It is easier to compile on the computation node not on the login node.
Since there are some difference about configuring/compiling on those
nodes. To configure and compile the *pmd*, first you need to login to a
computation node by doing `pjsub --interact`.

    $ pjsub --interact
    or
    $ pjsub --interact -L rscgrp=fx-interactive,node=1  <== in case of flow-fx@nagoya-u
    $ ./configure --prefix=$(pwd) FCFLAGS="-O3"
    $ cd pmd
    $ make pmd
    $ exit

!!! Note
    In case that the `configure` returns errors and exit without completing
    the configuration and the error message is related to cross compilation,
    you may need to add an option like `--host=sparc64` to the above command
    line.


### Fujitsu Fortran in CX400

In the case of Fujitsu Fortran compiler `mpifrt` in CX400,

    $ ./configure --prefix=$(pwd) FCFLAGS="-Kfast,parallel"


### Helios in Rokkasho-mura

It is Linux OS on Intel CPU, and the compilation seems to be basic one.
But one needs to add specific options as following,

    $ ./configure --prefix=$(pwd) FC=mpiifort FCFLAGS="-xAVX -O3 -ip -ipo -g -CB"

If you don\'t specify the `mpiifor` explicitly, `ifort` is set by
default and the compilation does not work correctly.


---

## Setup *nappy* (required for *fp.py*)

To use *nappy* in python program, it is required to add a path to
`nap/nappy` directory to the environment variable `PYTHONPATH`. In case
of `bash`, you can achieve this by adding the following line to
`~/.bash_profile`,


    export PYTHONPATH=${PYTHONPATH}:/path/to/nap


You can check whether the path to `nappy` is added to `PYTHONPATH` by
the following command, :

    $ python -c 'import nappy; print(nappy.__file__)'


---

## Tests

There are some examples in `nap/examples/` directory and all the examples contains reference outputs that should be written out when programs are correctly perfomed.

### Test for *pmd* program

There are three examples for *pmd* program,

- `nap/examples/pmd_W` -- a simple MD using a potential with given parameters.
- `nap/exampels/pmd_BVS_LLZO` -- an MD using screened Coulomb, Morse and angular potentials whose parameters are optimized using *fp.py* program.
- `nap/examples/pmd_DNN_SiO` -- an MD using a neural-network potential whose parameters are optimized using *fitpot* program.

In an example directory, you can run *pmd* program by using *mpirun* command as,

    $ mpirun -np 1 /path/to/pmd

You can test whether the *pmd* program is built correctly or does not degrade after development by comparing its standard output with `out.pmd.REF` file in the directory.

### Test for *fitpot* program

In the `nap/examples/fitpot_DNN_SiO` directory, you can test the *fitpot* program by using *mpirun* command as,

    $ mpirun -np 1 /path/to/fitpot

by comparing its standard output with `out.fitpot.REF`.

### Test for *fp.py* program

There are two examples for *fp.py* program,

- `nap/examples/fp_LZP` -- fit potential parameters using DF-matching mode (see [fp.py documentation](./fp.md#Reference data for fp.py))
- `nap/examples/fp_LAZP` -- fit potential parameters using whatever mode

In the `nap/examples/fp_LZP` directory, you can test the *fp.py* program as,

    $ python /path/to/nap/nappy/fitpot/fp.py --nproc=4 

by comparing its standard output with `out.fp.REF`.
