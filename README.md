# CIF_dREL: A dREL to Julia translator

## Introduction

dREL (J. Chem. Inf. Model., 2012, 52 (8), pp 1917–1925
DOI: 10.1021/ci300076w) is a machine-readable language for describing the
relationships between data names defined in a CIF (Crystallographic
Information Framework) dictionary.  Examples of dREL
use can be found in 
(the latest CIF core dictionary)[https://github.com/COMCIFS/cif_core/cif_core.dic].

This package is experimental.  Method and type names are subject to
change. It is likely to run a lot faster in the future as optimisations
are implemented.

## Installation

Install Julia.  At the Pkg prompt (ie after entering `]`) type
`add CIF_dREL`.  Simply put `using CIF_dREL` at the top of any
Julia code that uses methods from this package.

Note that CIF support is provided by the CrystalInfoFramework.jl package,
which you will probably also need to install in order to read in CIF
files.

Please advise of any difficulties in installation so that either these
instructions or the installation setup can be improved.

## Usage

1. After creating an ordinary Cifdic, ``define_dict_funcs(c::Cifdic)`` will
add all dREL functions found in the dictionary to that dictionary as
evaluated Julia definitions. Note that dREL functions are distinct from the
methods found inside definitions.
2. A ``dynamic_block`` is a CIF block that can evaluate missing datanames 
using dREL code found in the dictionary for that dataname, potentially executing long
chains of other evaluations.  The resulting values are **not**
stored in the block, but are cached.
3. ``derive(d::dynamic_block,s::String)`` will derive the value of dataname
``s`` based on other values in the block and dREL code found in the dictionary
associated with ``d``.
4. ``empty_cache!(d::dynamic_block)`` clears cached values from previous
derivations.

```julia
    p = Cifdic("/home/jrh/COMCIFS/cif_core/cif_core.dic")
    define_dict_funcs(p)    #add dREL Functions to dictionary
    n = NativeCif(joinpath(@__DIR__,"nick1.cif"))
    b = n["saly2_all_aniso"] #select a data block
    c = assign_dictionary(b,p) 
    db = dynamic_block(c) #create a dynamic block
    # 
    # (Re)evaluate an item
    #
    s = derive(db,"_cell.atomic_mass") #derive value
```

``test/drel_exec.jl`` contains simple demonstrations of how to
make use of dREL scripts found in dictionaries.
