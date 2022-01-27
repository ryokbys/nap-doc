# Coulomb potential (Ewald or short/screened)

Coulomb potential requires a parameter file `in.params.Coulomb`.
Parameters required in the file are different depending on `charges` and `terms` entries.

## Fixed charges given in the input

The basic format (fixed charges to be specified) of `in.params.Coulomb` is the following,

    charges  fixed
      Si   1.0
      O   -2.0
    interactions
      Si  Si
      Si  O
      O   O
    terms  short

    sigma  2.5

In this format, black lines are neglected. There are some keywords:

- `charges` : `fixed` or `variable` (or `qeq`) -- Followed by the lines of species charges, e.g., $q_1 = 1.0$ and $q_2 = -2.0$.
- `interactions` *(optional)* -- Followed by the pairs of species. If not specified, all the  interactions are taken into account.
- `terms` : `full`, `long` or `short`/`screened_cut` -- Either full Ewald terms, long-range term only, short-range term only, or short-term with smooth cutoff.
- `sigma` -- Width of the Gaussian charge distribution, which is related to the
  accuracy in case of Ewald method.

---

## Fixed charges computed assuming charge neutrality

If `charges  fixed_bvs` is given, the charges on species are computed using their formal charges and principal quantum numbers
as given by Adams and Rao[^Adams2011].
For example, the following `in.params.Coulomb` file is for Li-La-Zr-O system with species charges computed using their approach.

    terms   screened_cut
    charges   fixed_bvs 
       O       -2.0   0.66    2
       Li       1.0   1.28    2
       La       3.0   2.07    6
       Zr       4.0   1.75    5
    
    fbvs   1.000
    
    interactions 
       O   O
       Li  Li
       Li  La
       Li  Zr
       La  La
       La  Zr
       Zr  Zr

The meanings of entries in the four lines just below the `charges` entry are in the order:

1. species
2. formal charge
3. ionic radius used below to determine 
4. principal quantum number

And `fbvs` is $f$ parameter for $r_\mathrm{AB} = f\times (r_\mathrm{A} +r_\mathrm{B})$, which is the distance parameter between species A and B used in complementary error function in screened Coulomb potential as,

$$\phi_\mathrm{AB}(r_{ij}) = k\frac{q_i q_j}{r_{ij}} \mathrm{erfc} \left( \frac{r_{ij}}{r_\mathrm{AB}}\right)$$

The paper[^RK2020] describes how to optimize the ionic radii parameters, where all the principal quantum numbers are set to 1 in order to use formal charges for species.

---

## Variable charge or QEq

Coulomb potential can treat **variable charge** or **QEq** by specifying
`variable` or `qeq` to the `charges` keyword as shown below.

    charges  variable
      Si  4.7695    8.7893    0.0   0.0  2.4
      O   7.5405    15.8067   0.0  -1.2  0.0
    interactions
      Si  Si
      Si  O
      O   O
    terms   screened_cut
    sigma   2.5
    chgopt_method   damping
    codmp_method    damping
    fdamp_codmp     0.7
    conv_eps_qeq      1.0d-8
    nstp_codmp      100
    dt_codmp        0.005
    qmass_codmp     0.002
    qtot_codmp      0.0
    

Here, `charges variable` requires some following lines that have

    name,  chi,  Jii,  E0,  qlow,  qup

- `name`: name of the chemical species
- `chi`: electronegativity of the species (eV)
- `Jii`: hardness of the species (eV)
- `E0`: atomic energy (eV)
- `qlow`: lower limit of the charge of the species
- `qup`: upper limit of the charge of the species

And the meanings of the other parameters are listed below:

- `chgopt_method`: The method of charge optimization: `damping` or `FIRE`. And the `codmp` pre/postfix indicates that the variables are related to `chgopt_method`==`damping`.The `FIRE` is known to be efficient, but it is sometimes unstable.
- `fdamp_codmp`: Damping factor multiplied to velocities of charges every step in damping MD. (default: 0.7d0)
- `conv_eps_qeq`: Convergence criterion of energy difference (eV/atom) from the previous step or one step before the previous step. The difference from the step before the previous step is used because sometimes charges oscillate and do not converge otherwise. (Default: 1.0d-8)
- `nstp_codmp`: Max number of steps for damping MD. (Default: 100)
- `dt_codmp`: Time interval for damping MD in fs. (Default: 0.005)
- `qmass_codmp`: Mass of charge in the unit of proton mass. (Default: 0.002)
- `qtot_codmp`: Total charge of the system in the unit of $e$. (Default: 0.0)


[^Adams2011]: Adams, S. & Rao, R. P. High power lithium ion battery materials by computational design. Phys. Status Solidi  208, 1746–1753 (2011).
[^RK2020]: Kobayashi, R., Miyaji, Y., Nakano, K. & Nakayama, M. High-throughput production of force-fields for solid-state electrolyte materials. APL Materials 8, 081111 (2020)
