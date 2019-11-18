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
UPPMAX](detail-docs/running_programs_on_uppmax.md) *including the part about installing Miniconda*.

### Installing Miniconda

Instructions for installation of Conda is available
[here](https://conda.io/projects/conda/en/latest/user-guide/install/index.html). Choose *Miniconda*,
not *Anaconda* (Anaconda is Miniconda plus >700 packages that you might not need).

A somewhat confusing part of Conda is that it is available in two versions for each operating
system: One for Python 2.7 (Miniconda2) and one for Python 3.x (Miniconda3). In most cases you're
fine with *Miniconda3*, but on some older computers it might work because Python 3.x is not
installed, then you can choose Miniconda2; the functionality is, as far as I know, the same.

### Setting up channels

Conda packages are found in
[channels](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html), and it makes
life easier for you if you configure your Conda to use a few useful channels:

```
conda config --add conda-forge
conda config --add bioconda
```


### Creating an environment

Conda can keep potentially conflicting program packages apart by placing them in *environments*.
Although you don't have to work with environments and it might seem like an unnecessary complication
now, I *strongly recommend* that you use environments as it is likely to be very helpful later.

For the eemisdada2 scripts, I suggest you create an environment called eemisdada2, in which you
install all the packages you will need (except eemisdada2 which I haven't made into a Conda package
yet). Doing this is easy:

```
conda create -n eemisdada2 r-optparse r-tidyverse bioconductor-dada2
```

The above will create an environment called eemisdada2 that contains the three conda packages you
need for the eemisdada2 scripts.
