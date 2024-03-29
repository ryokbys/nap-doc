# Deep neural-network (DNN) potential

!!! Note
    The NN potentials (NN and NN2) were replaced with this `DNN` potential
    in January 2020. It is strongly recommended to use `DNN` potential
    instead of `NN2`, because `DNN` is a super-set of `NN2` and `NN2` is no
    longer maintenanced in the future.


DNN potential requires the following two input files in the working
directory.

-   `in.params.desc`: types of symmetry functions, parameters of the
    symmetry functions, their cutoff radii, and interacting pairs.
-   `in.params.DNN`: NN structures, number of layers, number of nodes
    for each layer, and weight values of the network.

These two files must be consistent such that the number of descriptors
must correspond to the number of inputs in the NN. The examples of these
files can be found in `pmd/force_params/DNN_??????` directories.

## in.params.desc

Currently (2021-09-30), there are two choices for the descriptor: Behler-Parrinello type symmetry functions and Chebyshev polynomial proposed by Artrith et al.

### Symmetry functions as a descriptor

!!! Note
    The format of `in.params.desc` file has changed from the previous one at
    around May 2019 to the style where species are written directly by their acronym
    name such as *Si* not by species-ID (digit 1-9). If you want to use the
    previous-format `in.params.desc`, you should modify it by replacing
    species-ID with species name.

The format of `in.params.desc` using the symmetry functions is like the following,

    2    20
    1    W   W   10.000    2.000
    1    W   W   10.000    3.000
    1    W   W   10.000    4.000
    1    W   H   10.000    2.000
    1    W   H   10.000    3.000
    1    W   H   10.000    4.000
    2    W   W   -0.900
    2    W   W   -0.800
    2    W   W   -0.700
    ...

- 1st line -- *number of speceis* and *number of descriptors*.
- The following lines have each descriptor information, the 1st entry is
  the type of descriptor, 2nd and 3rd are species of interaction pair,
  from the 4th to the end are parameters of the descriptor. The number
  of parameters depend on the type of descriptor.
- The descriptor type defined by a number is as follows, where the types less than 100 are two-body, and greater or equal to 100 are three-body.

    - `1` : Gaussian
    - `2` : cosine
    - `3` : polynomial
    - `4` : Morse type
    - `101` : angular-type1 (SW-type, not including fc(rjk))
    - `102` : angular-type2 (BP symmetry-function type, including fc(rjk))


### Chebyshev polynomial as a descriptor

The format of `in.params.desc` in case of Chebyshev polynomial is as follows,

    !  Chebyshev:  T
    !
    2  40
    2-body   10   5.0
    3-body   10   3.0
    Weight   Artrith
        1     1.0
        2    -1.0

- 1st and 2nd lines -- Comment lines in which Chebyshev flag should be set `T`.
- 3rd line -- *number of species* and *number of terms*
- 4th line -- declaration of *2-body* terms, *num of 2-body terms*, and *cutoff radius*
- 5th line -- declaration of *3-body* terms, *num of 3-body terms*, and *cutoff radius*
- 6th line -- Specify the type of weight.
- after 6th line -- *species ID* and *weight for the species*

The *number of terms* should be 2 times (*num of 2-body terms* + *num of 3-body terms*) in case of *num of species* is greater than 1, and it should be (*num of 2-body terms* + *num of 3-body terms*) otherwise.

## in.params.DNN

`in.params.DNN` file should have the following format.

    !  sigtype: 2
    ! 
       3   20   10  10  5
     -3.64106023330479E-001 -1.0000E-01  1.0000E-01
     -2.01340565152879E+000 -1.0000E-01  1.0000E-01
     ... (number of these lines should be the same as the number of NN weights)

- Lines starting with `!` are comment lines. If any of special keyword is found just after `!`, an option will be passed to the program.
- The 1st entry of the 1st line following comments is the number of hidden layers in the NN.
- The 2nd entry is the number of nodes in 0-th layer, which is called input layer.
- The following digits are the number of nodes in hidden layers. The number of these digits must be the same as the number of hidden layers given by the 1st entry.
- Following lines include weight value, lower and upper bounds. The number of lines must be equal to or more than the number of NN weights, which contains biases.
  The lower and upper bounds are only used in *fitpot* not in *pmd*.
