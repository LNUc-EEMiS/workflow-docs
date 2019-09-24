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
