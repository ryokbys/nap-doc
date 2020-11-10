# nappy -- NAP PYthon utilities

*nappy* is a set of python utilities. It is contained in `nap/nappy/`
directory, but currenly it is not exactly a python module. Users may
have to use those utilities by calling them directly from the shell.

## Setup

See [Install](./install.md).

-----

## Quick start

### `napsys.py` command

Once *nappy* is installed correctly, you can use `napsys.py` as an utility command.

It is recommended to make symlink of `/path/to/nap/nappy/napsys.py` in some directory lised in the PATH environment,
so that it is found by typing just `napsys.py`.

The following commend will give you some information about the system described in `pmdini`.
```bash
$ napsys.py analyze /path/to/nap/example/test_W/pmdini
```
If you want to convert the file between some formats available in *nappy*,
the following command convert `infile` to the format guessed from the filename `outfile`,
such as `POSCAR`, `dump_0`, or `pmd_0`.
```bash
$ napsys.py convert infile outfile
```
To show the help message,
```bash
$ napsys.py -h
```

---

### nappy on jupyter or ipython
do the following code on *jupyter notebook* or *ipython*,

```python
from nappy.napsys import NAPSystem, analyze
nsys = NAPSystem(fname='/path/to/nap/example/test_W/pmdini')
analyze(nsys)
```

Above code will show the following result. :

    a1 vector = [    10.254,      0.000,      0.000]
    a2 vector = [     0.000,      9.613,      0.000]
    a3 vector = [     0.000,      0.000,      9.613]
    a =     10.254 A
    b =      9.613 A
    c =      9.613 A
    alpha =   90.00 deg.
    beta  =   90.00 deg.
    gamma =   90.00 deg.
    volume=    947.617 A^3
    number of atoms   =  54


---

## Read and write files

To read atomic structures from files of several formats,

```python
nsys = NAPSystem(fname='POSCAR')
nsys = NAPSystem(fname='structure.dat', ffmt='xsf', specorder=['Al','O'])
```

Current available formats are:

- `pmd`: file format specific for `pmd` program
- `POSCAR`: VASP POSCAR file
- `dump`: LAMMPS dump file
- `xsf`
- `akr`: file format for Akira viewer
- other formats readable by [ASE](https://wiki.fysik.dtu.dk/ase/) .

And to write the structure to a file,

```python
nsys.write('POSCAR')
nsys.write('pmdini')
nsys.write_dump()
```

The `write` function will parse file name and choose appropriate file
format from the name.
