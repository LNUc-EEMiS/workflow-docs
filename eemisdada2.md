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

## Clone the eemisdada2 repository and make sure scripts are available

Unfortunately, I haven't yet made my scripts available in a Conda package. You therefore have to
fetch them from GitHub by cloning the repository and make sure they are available.

I suggest you create a subdirectory called `dev` in your home, where you clone the repository.

```
cd # cd into your home directory
mkdir dev
cd dev
git clone https://github.com/erikrikarddaniel/eemisdada2.git
```

The next step is to make sure you can run the scripts. To do this, you will place *symbolic links*
to the scripts in a directory called `bin` in your home directory, and then make sure that the
operating system looks for programs there.

```
cd
mkdir bin
cd bin
ln -s ../dev/eemisdada2/src/R/*.R .
cd
which dada2wf.R # Check if the scripts are found
```

Everything is correct if the last command output something like:

```
~/bin/dada2wf.R
```

If it doesn't, do this:

```
echo "PATH=~/bin/:$PATH" >> ~/.bash_profile
```

Log out and log in again and run the `which dada2wf.R` command to check if the scripts are found.
