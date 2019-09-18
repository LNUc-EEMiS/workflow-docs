# *De novo* assembly and annotation of bacterial metatranscriptomes

This document describes how to perform an annotation of a (mostly) bacterial metatranscriptome
starting with a *de novo* assembly.

It depends on library makefiles from
[biomakefiles](https://github.com/erikrikarddaniel/biomakefiles) and the following programs:

- MegaHIT

- Bowtie2

- Samtools

MegaHIT, Bowtie2 and Samtools are all installed as modules at UPPMAX clusters, all named as you
expect but with only small letters. Choose the newest version.

## Biomakefiles

[Biomakefiles](https://github.com/erikrikarddaniel/biomakefiles) is a repository of library
*makefiles* that makes it easier to run programs and also documents some important facts about
programs that were run to produce a certain result.  (There are now better tools for the same
purpose, in particular [Snakemake](https://pypi.org/project/snakemake/), but we have not yet
rewritten workflows, so until we do, you'll have to make do with *Make*.)

More documentation will eventually be found [here](biomakefiles.md).

To use biomakefiles, you need to clone the repository somewhere, and then create a symlink to this
in your project.

```
cd ~
mkdir dev
git clone https://github.com/erikrikarddaniel/biomakefiles.git
cd ~
cd projects/my_metat
ln -s ~/dev/biomakefiles .
```

# Workflow

The overall workflow runs like this:

1. Remove sequencing adapters and quality trim reads

2. Remove stable RNA (e.g. rRNA, tRNA)

3. Assemble reads with MegaHIT

4. Map reads back to assembly to allow quantification

5. Call ORFs

6. Annotate, functionally and taxonomically, ORFs

(This documentation is not finished yet for all the steps.)

## Preparing your sequence files

First, you need to run [quality trimming and cut adapters](quality_trim_and_cutadapt.md).

## Remove stable RNA

TO BE DOCUMENTED.

## Assembly with MegaHIT

Create a new directory with symlinks to quality checked fastq files with file names ending with
`.r1.fastq.gz` and `.r2.fastq.gz` for forward and reverse respectively, and create a file called
`Makefile` looking like this:

```{make}
include ../../../biomakefiles/lib/make/makefile.megahit

MEGAHIT_OPTS = --num-cpu-threads 20 --memory 0.95
```

The path after `include` should be a *relative* path to where you have your biomakefiles, i.e. the link you created in
the root directory of your project. The number of `../` will hence depend on how many directories down you are from the
root directory.

The `MEGAHIT_OPTS` line allows you to set any parameters that MegaHIT accepts. To see available options:

```
module load megahit
megahit --help
```

To test that everything is setup correctly:

```
make -n all.megahit_out
```

The above should *not* return an error message like `make: *** No rule to make target `all.megahit_out'.  Stop.`.

If all looks good, submit to the [SLURM queue](http://www.uppmax.uu.se/support/user-guides/slurm-user-guide/):

```
sbatch -A $uproject -J $prefix:megahit -p node -N 1 --wrap "make all.megahit_out" --mail-user=$email --mail-type=ALL -t 10-00:00:00
```

Replace `$uproject` with your project name, `$prefix` with whatever you want to identify your jobs
with and `email` with the email address you want messages to. (I set environment variables for this;
`email` in my `.bash_profile`, the other two in the `.screenrc` file for the project.)

## Map back reads to the assembly

Mapping reads back to the assembly is useful for at least two purposes. First of all, it allows us
to quantify how much of our data (the reads) were incorporated in the assembly. If a large
proportion of reads do not map back to the assembly, the assembly is not very representative.
Secondly, after calling ORFs (genes) one can use the mapping results to quantify the ORFs, leading
to the tables with genes, samples and counts that we need for the biological analysis.

Mapping in general will be described in [mapping reads to sequences](mapping_reads_to_sequences.md),
so here I'm only giving a short description of how to map metaT reads to a MegaHIT assembly.

Create a subdirectory to the `all.megahit_out` directory created by the assembly process described
above, and place symbolic links to the read files plus the output from the assembly there:

```
cd all.megahit_out
mkdir bowtie2
cd bowtie2
ln -s ../../*.r?.fastq.gz .
ln -s ../final.contigs.fa my_metaT_assembly.fna
```

Write a `Makefile` that looks something like this (the paths to where `makefile.bowtie2` and
`makefile.samtools` are located are likely to be different for you):

```{make}
include ../../../biomakefiles/lib/make/makefile.bowtie2
include ../../../biomakefiles/lib/make/makefile.samtools

# Set this to the number of cores you have (20 for rackham, 16 for snowy)
BOWTIE_ALIGN_OPTS = -p 20

BOWTIE_INDEX = my_metaT_assembly
```

Before you can map, you need to create the database that Bowtie2 reads. This is done with `make
my_metaT_assembly.bowtie2index` (do a "dry run" with `make -n` like above):

```
# Load the required modules
module load bowtie2
module load samtools
# Send the command to the queue
sbatch -A $uproject -J $prefix:megahit -p core -n 4 --wrap "make my_metaT_assembly.bowtie2index" --mail-user=$email --mail-type=ALL -t 10-00:00:00
```

When the above job finishes, you can start the mapping:

```
sbatch -A $uproject -J $prefix:megahit -p node -N 1 --wrap "make all_pe_sorted_bams" --mail-user=$email --mail-type=ALL -t 10-00:00:00
```
