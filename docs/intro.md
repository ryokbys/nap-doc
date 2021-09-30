# Introduction

The main program in this package, *pmd*, is an acronym of **parallel molecular dynamics**,
that is, molecular dynamics (MD) using spatial decomposition technique on
parallel (ditributed-memory) computers.

The main features of *pmd* are the following:

- several interatomic potentials for solid state systems are available;
- **Deep neural-network (DNN) interatomic potential**;
- **QEq** or **variable charge** Coulombic potential;
- MPI parallel computation using spatial decomposition technique and hybrid parallelization using OpenMP;
- efficient searching of neighbor atoms using linked-cell list method;
- structure relaxation using simple velocity damping or **FIRE** algorithm;
- thermostats: Berendsen and Langevin;
- barostat: Berendsen;
- **variable-timestep** MD for high-energy ion-bombardment simulation;
- **non-equilibrium MD (NEMD)** for heat flux and/or ion flux simulation;
- **two-temperature model MD (TTM-MD)** for laser-ablation simulation.

Since this program has been developed for the purpose of personal
research tool, there are not so many functionalities. And there are some
(open source) MD programs that can do almost the same thing that *pmd*
can do. But there are some features only *pmd* or the parent packange
**nap** can do. Please feel free to contact me to ask anything if you
want to use *pmd* or **nap** for your specific purpose.

Bug reports and questions about *pmd* and **nap** are welcome, but I am
afraid that I might not be able to respond all the reports or questions.

