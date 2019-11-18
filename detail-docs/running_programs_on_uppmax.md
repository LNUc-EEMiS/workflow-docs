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

You 
