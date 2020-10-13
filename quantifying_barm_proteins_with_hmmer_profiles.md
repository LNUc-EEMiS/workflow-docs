# Finding and quantifying BARM proteins with HMMER profiles

This document describes how to search for proteins (ORFs) in the BARM (Baltic Sea metagenomes)
database using protein HMMER profiles.

## Prerequisites

This document assumes you have collected a *set of HMMER profiles* from Pfam, TIGRFAM, EGGNOGs or
similar and have created a *table with at least two columns*: protein and profile.
The table should be a *tab separated text file*.

As you will run programs at Rackham (or another UPPMAX cluster), checkout [Installing and running
programs on UPPMAX](detail-docs/running_programs_on_uppmax.md).
Pay particular attention to the description of the modules system and section describing how you
install your own programs via Conda and make sure you follow the link to the general description of
Conda at the bottom of the page for UPPMAX.

### Programs

After you have installed Conda following the instructions in the links above, you should create an
environment for the task I'm describing here (remember to follow the instructions for how to add
the `bioconda` and `conda-forge` channels to your config).

```
conda create -n barm-hmmer -c matricaria hmmrank hmmer
```

## Running `hmmsearch` against the BARM database

I'm assuming you are working in a directory where you have the table in `tsv` format and that you
have collected all the HMM files in the table in this directory.
I suggest you use the file suffix `.hmm` in *small letters* for the HMM files.
If the files end with something else, e.g. `.txt`, when you download them, rename.

In short, you will first run all the searches with `hmmsearch`, then summarize and filter the 
results with `hmmrank.r`.

### The BARM database

The BARM database is a collection of contigs taken from Baltic Sea metagenomes (Alneberg et al.
2020). 
We have called ORFs, i.e. identified potential protein coding sequences in the data.
The data is available in the UPPMAX (Rackham and Snowy clusters):

```
/proj/snic2020-16-76/nobackup/projects/BARM
```

What you're after to specify as the database when you run `hmmsearch` is the fasta file with amino
acid sequences for ORFs:

```
/proj/snic2020-16-76/nobackup/projects/BARM/WAITING_FOR_ANDERS_ANDERSSON
```

### HMMER and `hmmsearch`

HMMER is a suite of tools for construction and use of *H*idden *M*arkov *M*odels (HMM or "profile") 
as search tools.
The most efficient tool if you have a set of profiles is `hmmsearch` which takes a single HMM and a
fasta file with protein sequences ("database") and identifies the sequences that match the HMM.
Besides the HMM and the database (in that order), `hmmsearch` takes a number of options and
parameters (use `hmmsearch -h`).
The most important of these parameters for you is the `--tblout` that will lead to output files that
suit the `hmmrank.r` program that you will use to rank and filter your search results.
You *do not have to set* any threshold parameter like E value or bitscore, as that filtering is
performed by `hmmrank.r`.

### `hmmrank.r`

`hmmrank.r` is an R script that reads a number of `.tblout` files from `hmmsearch`, the table of
protein to profile mappings and a table of inclusion thresholds.
You can get help for the program: `hmmrank.r --help`.
It has the following purposes:

1. Apply *bitscore* cutoffs, which can either be global, i.e. the same for all profiles, or specific
   to profiles.
   In the latter case, the cutoffs are specified in a tab separated text file and input to the
   program with the `--scorefile` parameter.
2. Map profile hits to protein names...
3. Rank the hits...

### Protocol
