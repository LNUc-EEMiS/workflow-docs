# Assembly of metaG or metaT sequences with MegaHIT

Create a new directory with symlinks to quality checked fastq files with file names ending with
`.r1.fastq.gz` and `.r2.fastq.gz` for forward and reverse respectively (see the ["File suffices"
section of "Make and `biomakefiles`](biomakefiles.md)), and create a file called `Makefile` looking
like this:

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
