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

It is recommended to make symlink of `/path/to/nap/nappy/napsys.py` in some directory lised in your PATH environment,
so that it is found by just typing `napsys.py`.

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

Some modules `nappy` can be called within *jupyter notebook* or *ipython*, for example,

```python
import nappy
from nappy.napsys import analyze
nsys = nappy.io.read('/path/to/nap/examples/pmd_W/pmdini')
print(nsys)
```

Above code will show the following result.

```
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
```

---

## Read and write files

To read atomic structures from files of several formats,

```python
import nappy
nsys = nappy.io.read('POSCAR')
nsys = nappy.io.read(fname='structure.dat', format='xsf')
```

Current available formats are:

- `pmd`: file format specific for `pmd` program
- `POSCAR`: VASP POSCAR file
- `dump`: LAMMPS dump file
- `xsf`
- `akr`: file format for Akira viewer

And to write the structure to a file,

```python
import nappy
nappy.io.write(nsys,'POSCAR')
nappy.io.write(nsys,'pmdini')
nappy.io.write(nsys,'dump')
```

The `write` function will parse file name and choose appropriate file
format from the name.


---

## Convert to/from ASE atoms

Conversion between *nappy.napsys.NAPSystem* object and *ASE atoms* object can be done with the following code,

```python
import nappy
nsys = nappy.io.from_ase(atoms)
atoms = nsys.to_ase_atoms()
```

---

## Call *pmd* from python

If `pmd_wrapper` was compiled using f2py, *pmd* can be called from python as following.

    import nappy
    nsys = nappy.io.from_ase(atoms)
    pmd = nappy.pmd.PMD(nsys)  # <== Set napsys as an atomic system used in pmd
    pmd.load_inpmd()           # <== assuming there is in.pmd file in the working directory
    pmd.run()                  # <== Run MD
    nsys = pmd.get_system()    # <== Get the new system that contains MD results
    nsys.get_potential_energy()
      -11389.735335774534

*nappy.pmd.PMD()* class provides some functions for performing *pmd* using *napsys* object.
*PMD* assumes that there are some input files, e.g. `in.params.XXX` used to perform *pmd*, in the working directory.

---

## Viewing the system on jupyter notebook

The *nappy* can visualize the system easily on jupyter notebooks. By default, the *nappy* uses the Py3Dmol as a backend and it should be installed.

```python
nsys.view()
```

It is not recommended to visualize the system with a huge number of atoms. Use other visalization apps instead.
