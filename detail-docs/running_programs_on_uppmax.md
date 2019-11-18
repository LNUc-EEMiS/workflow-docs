# Installing and running programs on UPPMAX

The SNIC super computer center UPPMAX is running *clusters* of Linux computers. Since the clusters
are running Linux, they are used in a very similar way to a standalone Linux computers, but there
are differences mostly due to the fact that they are used by many users running many different kinds
of programs. While many programs are already available, you are likely to need to install programs
yourself sometimes. Since you don't have super user rights, installing programs is a little bit
different than on a standalone Linux computer.

## Standard programs ("utilities")

Standard UNIX programs, like `more`, `gzip` and `sed` are available directly.

## Specialized programs available as modules

Specialized programs in general are available via the [module
system](https://www.uppmax.uu.se/resources/software/module-system/) at UPPMAX. This means you get
access to a program after "loading" a module; the below gives you access to the `gcc` compiler:

```
module load gcc
```

To check which modules you have loaded:

```
module list
```

To search for modules:

```
module spider program
```

Where `program` is the name of the program you're looking for.

There is a lot of information at UPPMAX about modules (including a [list of installed
programs](https://uppmax.uu.se/resources/software/installed-software/); just search the UPPMAX web site.

### Specialized bioinformatics programs

There is a special module that *contains other modules* that gives you access to bioinformatic
programs. Load that:

```
module load bioinfo-tools
```

Then, but only then, can you load many bioinformatics programs, e.g. `bowtie2` (a mapping program):

```
module load bowtie2
```

## Installing your own programs

You can install your own programs at UPPMAX, but only in your home directory (or any projects
directory you have access to, but home is more common). UPPMAX has a very short [guide to install a
typical UNIX
program](https://uppmax.uu.se/support/faq/software-faq/can-i-install-my-own-software-on-the-uppmax-systems/).

### Installing your own programs with *Conda*

A better alternative is to use [Conda](https://docs.conda.io/en/latest/index.html). There is a
module for Conda -- **don't use that**, but install it yourself.

### Installing Miniconda

Instructions for installation of Conda is available
[here](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html). Choose *Miniconda*,
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
conda config --add --env channels conda-forge
conda config --add --env channels bioconda
```

### Creating an environment

Conda can keep potentially conflicting program packages apart by placing them in *environments*.
Although you don't have to work with environments and it might seem like an unnecessary complication
now, I *strongly recommend* that you use environments as it is likely to be very helpful later.

Typically, you would create an environment either for a single program, or for a set of programs you
often use together, e.g. all programs you use when you run assembly and annotation.

Create an environment:

```
conda create -n my-env
```
