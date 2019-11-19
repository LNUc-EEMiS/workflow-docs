# Denoising amplicon sequences with script(s) in the eemisdada2 repository

The [eemisdada2](https://github.com/erikrikarddaniel/eemisdada2.git) repository contains R scripts
that implements the [DADA2 workflow](https://benjjneb.github.io/dada2/index.html) for denoising of
amplicon reads. This document describes how to install and run the scripts with dependencies.

## Installing necessary programs

### Get Conda and set up an eemisdada2 environment

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

### Clone the eemisdada2 repository and make sure scripts are available

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

## Running the script(s)

With the above done, you're ready to start denoising your reads with the `dada2wf.R` script. The
script runs all steps of the process: filtering and trimming reads, calculating error profiles,
denoising reads, merging pairs and filtering out likely chimeric sequences. It will produce a table
with named sequences and a fasta file with sequences. However, quite a lot of parameters can be set
and not all of them have default values. You can get a list of all parameters like this:

```
dada2wf.R --help
```

Here, I will explain some of the key parameters and give some guidance as to what values to use for
them.

* `--indir` sets the path to where your input files are. The default is your current working
  directory, which makes it easy to run the script by cd:ing into a directory with the read files. I
  actually suggest you do not work in the directory where you have the reads, but create a new
  directory with *symbolic links* to the actual files. As an example, assuming you have your reads
  in directories as they are delivered by SciLifeLab, i.e. one directory per sample, you would do
  something like:

```
mkdir dada2
cd dada2
ln -s *path_to_delivery_directory*/P*NNN*/*/*/*/*.fastq.gz .
```

* `--trimleft` and `--trunclen` sets very imporant values for the filtering step. `--trimleft=21,21`
  tells the script to cut 21 base pairs from the start of each read to get rid of primers, typical
  for 16S V3V4 primers. `--trunclen=290,210` will trim sequences down to 290 bp and 210 bp for
  forward and reverse reads respectively. These are the values I use for V3V4 reads, but you should
  consult MultiQC output (delivered by SciLifeLab) to determine reasonable values. Requiring longer
  sequences, will decrease the number of reads that pass the filter, as no sequence shorter than the
  cutoff will pass.

* `--fwdmark` and `--revmark` are used by the script to differentiate between files with forward and
  reverse reads respectively. The defaults, `.r1` and `.r2` respectively, work if your forward reads
  are in files with ".r1" somewhere in their names and reverse reads have ".r2" in their names. This
  is not very common, and more standard values are `_R1_` and `_R2_` respectively (works with files
  delivered by SciLifeLab).

* `--nsamples` sets the number of samples that will be used to estimate error models from. The
  default is to use an eighth of the total number of samples, which works well for 96 samples. Aim
  for all samples, if you have up to around 20 samples.

* `--verbose` turns on quite a lot of reporting of the progress of the script. I *strongly
  recommend* that you turn this on, as it will tell you such things as the version of the script
  plus the DADA2 library, as well as some statistics about how many reads and sequences make it
  through the process. In the example below, I will show you how to direct this information to a
  file that you can refer to later.

I advice you to look through the other options as well, although the description is sometimes short.
In most cases there's more information to be had from the DADA2 documentation, although often with
slightly different names. The documentation is work in progress...

### Example command line

Assuming you have created a directory `dada2` in which you have collected symlinks to your raw data
files, you could run the script like this:

```
dada2wf.R --trimleft=21,21 --trunclen=290,210 --fwdmark=_R1_ --revmark=_R2_ --verbose
```

To send this to a Slurm queue e.g. on an UPPMAX cluster, you could write a submission script like
this:

```
#!/bin/bash -l
 
#SBATCH -A p2012999
#SBATCH -p core
#SBATCH -n 8
#SBATCH -t 10-00:00:00
#SBATCH -J dada2

dada2wf.R --trimleft=21,21 --trunclen=290,210 --fwdmark=_R1_ --revmark=_R2_ --verbose
```

If you saved the above in a file called `dada2_submit.sbatch` you would submit like this:

```
sbatch dada2_submit.sbatch
```

You could also submit the script directly on the command line:

```
sbatch -A p2012999 -p core -n 8 -t 10-00:00:00 -J dada2 --wrap"dada2wf.R --trimleft=21,21 --trunclen=290,210 --fwdmark=_R1_ --revmark=_R2_ --verbose"
```
