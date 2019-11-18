# Denoising amplicon sequences with script(s) in the eemisdada2 repository

The [eemisdada2](https://github.com/erikrikarddaniel/eemisdada2.git) repository contains R scripts
that implements the [DADA2 workflow](https://benjjneb.github.io/dada2/index.html) for denoising of
amplicon reads. This document describes how to install and run the scripts with dependencies.

## Get Conda and set up an eemisdada2 environment

A number of dependencies for the scripts are available via
[Conda](https://docs.conda.io/en/latest/index.html), a program that manages other programs and
software libraries. Installing the dependencies with Conda is in most cases the easiest option. Read
the, relatively short, [user
guide](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html).

If you're running on UPPMAX, read [running programs on
UPPMAX](detail-docs/running_programs_on_uppmax.md) *including the part about
installing your own programs with Conda*.

After you have installed your own Conda, I suggest you create an environment called eemisdada2, in
which you install all the packages you will need (except eemisdada2 which I haven't made into a
Conda package yet). Doing this is easy:

```
conda create -n eemisdada2 r-optparse r-tidyverse bioconductor-dada2
```

The above will create an environment called eemisdada2 that contains the three conda packages you
need for the eemisdada2 scripts.
