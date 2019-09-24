# *De novo* assembly and annotation of bacterial metatranscriptomes

This document describes how to perform an annotation of a (mostly) bacterial metatranscriptome
starting with a *de novo* assembly.

It depends on library makefiles from
[biomakefiles](https://github.com/erikrikarddaniel/biomakefiles) and the following programs:

- Cutadapt

- Sickle

- Bowtie2

- Samtools

- MegaHIT

MegaHIT, Bowtie2 and Samtools are all installed as modules at UPPMAX clusters, all named as you
expect but with only small letters. Choose the newest version.

## Biomakefiles

See [Make and `biomakefiles`](biomakefiles.md) for a, so far, short introduction.

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
*NOT DONE YET.*

## Remove stable RNA

TO BE DOCUMENTED.

## Assembly with MegaHIT

See [Assembly of metaG or metaT sequences with MegaHIT](metaG_and_metaT_assembly_with_megahit.md).

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
