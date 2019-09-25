# Make and `biomakefiles`

[Biomakefiles](https://github.com/erikrikarddaniel/biomakefiles) is a repository of library
*makefiles* that makes it easier to run programs and also documents some important facts about
programs that were run to produce a certain result.  (There are now better tools for the same
purpose, in particular [Snakemake](https://pypi.org/project/snakemake/), but we have not yet
rewritten workflows, so until we do, you'll have to make do with *Make*.)

To use biomakefiles, you need to clone the repository somewhere, and then create a symlink to this
in your project. What I do is that have a symlink to my copy of `biomakefiles` in the root directory
of the project. Follow these commands:

```
# 1st time: Clone the biomakefiles repository:
cd ~
mkdir dev
git clone https://github.com/erikrikarddaniel/biomakefiles.git
# Skip first time, but then update the repository for all later projects
cd biomakefiles
git pull
# Move to your home directory and cd into your project directory
cd ~
cd projects/my_metat
# Create a link to your copy of the repository
ln -s ~/dev/biomakefiles .
```

## Conventions in `biomakefiles`

You need to follow a few rules to get Make with `biomakefiles` to work properly and efficiently. 

### File suffices

The rules in `biomakefiles` require that file names end -- have correct suffices -- in a certain
way. Once you have started a project and run the first step, files will automatically get correct
names, unless you rename them, but for the first step, you need to get suffices right. In most cases
you will start with paired fastq files, and they should preferably be gzipped (end with `.gz`). File
names differ between sequencing machines and centers. One common name pattern is that forward reads
are placed in files with `_R1_` or just `_1_` somewhere in their names, whereas reverse reads have
`_R2_` or `_2_`. To work with `biomakefiles` forward reads *must* be placed in files ending with
`.r1.fastq.gz` and reverse reads in files ending with `.r2.fastq.gz` (the `.gz` is required for most
tasks, voluntary for some and doesn't work for a few).

*Do not rename* your original files, the best way to create files with correct names is to create
*symbolic links*. If you have your original files in the `origfiles` directory parallel to the
current one, the way to create symlinks in this directory is:

```
ln -s ../origfiles/name_R1_1.fastq.gz name.r1.fastq.gz # Forward
ln -s ../origfiles/name_R2_1.fastq.gz name.r2.fastq.gz # Reverse
```
