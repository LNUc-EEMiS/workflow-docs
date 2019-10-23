# Assembly, binning and annotation of bacterial metatgenomes

This document describes how to perform an annotation of a (mostly) bacterial metagenome.

It depends on library makefiles from
[biomakefiles](https://github.com/erikrikarddaniel/biomakefiles) and the following programs:

- Cutadapt

- Sickle

- Bowtie2

- Samtools

- MegaHIT

All are installed as modules at UPPMAX clusters, all named as you expect but with only small
letters. Choose the newest version.

## Biomakefiles

See [Make and `biomakefiles`](detail-docs/biomakefiles.md) for a, so far, short introduction.

# Workflow

The overall workflow runs like this:

1. Remove sequencing adapters and quality trim reads

2. Assemble reads with MegaHIT

3. Map reads back to assembly to allow quantification

4. Bin the resulting contigs and calculate key statistics for bins

5. Call ORFs

6. Annotate, functionally and taxonomically, the bins

(This documentation is not finished yet for all the steps.)

## Preparing your sequence files

First, you need to run [quality trimming and cut adapters](detail-docs/quality_trim_and_cutadapt.md).

## Remove stable RNA

TO BE DOCUMENTED.

## Assembly with MegaHIT

See [Assembly of metaG or metaT sequences with MegaHIT](detail-docs/metaG_and_metaT_assembly_with_megahit.md).
