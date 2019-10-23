# Quality trimming and cutting adapters

The purpose of the steps described in this document is to get rid of low quality
bases and get rid of *adapters*, sequence parts that emanate from the library
preparation process. 

The general procedure is to start with removing the adapters and then trimming
low quality parts of the sequences. To monitor the success, it's good to start
by running [FastQC/MultiQC](fastqc_multiqc.md) on the original reads, run the
two filtering steps and then run FastQC/MultiQC on the resulting reads. This
allows you to compare the quality of libraries before and after to ensure that
the quality filtering and trimming you did was successful.

## Tools: `cutadapt` and `sickle`

There are a number of tools for both steps available. In this document I'm
describing how to run [cutadapt](https://github.com/marcelm/cutadapt) to trim
away adapters and [sickle](https://github.com/najoshi/sickle) to quality trim.

As with all other procedures described here, this uses Make and
[biomakefiles](biomakefiles.md) which requires that filenames end in defined
ways and suggests a certain way of working, primarily that you perform each step
in its own directory with symlinks pointing to input files.

## Workflow

I suggest using a directory structure like this:

```
samples/
qc/fastqc
qc/cutadapt
qc/sickle
qc/sickle/fastqc
```

In `samples` you collect symlinks to the original gzipped fastq files, ending
with `.r1.fastq.gz` and `.r2.fastq.gz` for forward and reverse reads
respectively.

### 1. Running FastQC/MultiQC on the original sequences

*N.B.* This step is usually performed by SciLifeLab/NGI before they deliver the
sequence files, so is not necessary if you sequenced at NGI.

In `qc/fastqc` you link to the links in `samples` and create a `Makefile`
looking like this:

```{make}
include ../../biomakefiles/lib/make/makefile.fastqc
```

(*Note* that the above assumes the above directory structure and the presence of
a link to the `biomakefiles` repository in the root directory.)

Make sure you have the FastQC and MultiQC programs installed. (At UPPMAX there
are modules you can load. Otherwise, install using
[Conda](https://docs.conda.io/en/latest/miniconda.html); I suggest you create an
environment, called e.g. seqqc, containing
[FastQC](https://anaconda.org/bioconda/fastqc),
[MultiQC](https://anaconda.org/bioconda/multiqc),
[cutadapt](https://anaconda.org/bioconda/cutadapt) and
[sickle](https://anaconda.org/bioconda/sickle-trim).)

When both programs are available, run:

```
make all_fastqc
```

On UPPMAX, run through the queue using *N* cores (set *N* <= the number of
samples; *note* that N is in two places in the command):

```
sbatch -A $uproject -J fast-multiqc -p core -n N --wrap "make -j N all_fastqc" --mail-user=$email --mail-type=ALL -t 10-00:00:00
```

### 2. Cutting adapters with `cutadapt`

### 3. Quality trimming with `sickle`
