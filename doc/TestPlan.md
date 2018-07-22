# Test Plan 

This document outlines a plan for testing the distribution and 
management of Biocondcutor packages through Conda and Bioconda.

## Table of Contents

- [Bioconda Overview](#BiocondaOverview)
  - [Terms](#Terms)
  - [Packages](#Packages)
  - [Recipes and build system products](#RecipesAndBuildSystemProducts)
- [Testing](#Testing)
  - [Conda package manager](#CondaPackageManager)
  - [Docker](#Docker)
  - [Common tests](#CommonTests)

<a name="BiocondaOverview"></a>
## Bioconda Overview

<a name="Terms"></a>
### Terms

Conda is a package manager designed to be a cross-platform version of
something like `apt-get` or `dnf`. It installs packages from its own
repositories called channels and installs them into a Conda environment
on the local machine. Bioconda is a channel specializing in bioinformatics 
software. Other terms that can be confusing are Anaconda and miniconda.
These last two are distributions and not package managers.

* Package managers:

  Conda:
  General package manager

  pip:
  Python package manager

* Distributions:

  Anaconda:
  Conda + Python + Full distribution of software in PyData ecosystem

  miniconda:
  Conda + Python

<a name="Packages hosted"></a>
### Packages

The Bioconda channel hosts packages based on R, Java, Perl, C/C++, Haskell,
and various command-line tools. See the list under 'Guidelines for Bioconda
recipes':

https://bioconda.github.io/guidelines.html

<a name="RecipesAndBuildSystemProducts"></a>
### Recipes and build system products
Packages are added to the Bioconda channel by submitting a 'recipe' to GitHub:

https://github.com/bioconda/bioconda-recipes

The Bioconda build system uses these recipes to produce packages and docker 
containers. 

<a name="Testing"></a>
## Testing

<a name="CondaPackageManager"></a>
### Conda package manager

* Install Conda, point to the `bioconda` channel and install packages
  into the virtual environment. Where are packages installed? How
  is an 'uninstall' handled?
 
  NOTE: It sounds like the order of adding channels
  is important:

      conda config --add channels defaults
      conda config --add channels conda-forge
      conda config --add channels bioconda

* What is the `r` channel and how does it play with `conda-forge` and
  `bioconda` channels?

https://github.com/bioconda/bioconda-recipes/issues/8042

* Does the Bioconda build system reflect the same build failures as the
  as compared to the Bioconductor builds? If not, what are they doing
  different?

* Activate Conda R and use `BiocManager::install()`. Where are the
  packages installed? Can this be controlled or remided with 
  `R_LIBS_USER` or setting a location in `BiocManager::install(..., loc=...)`?

* `conda install` a file or list to install multiple packages.

* What happens in the Bioconda build system when a system dependency is not
  available? Does `R CMD INSTALL` skip or error on code that requires
  that system dependency, i.e., is the package 'installable' but not fully
  functional?

* Run through 'Common Tests'.

<a name="Dockers"></a>
### Dockers

The Bioconda term for a Docker is 'BioContainer'.

* Explore how to install a single BioContainer.

* Is R already installed in the individual Dockers containing Bioconductor 
  packages? 

* Is Conda installed by default in these BioContainers and if yes, what
  channels is it pointing to?

* How does layering work?
  - Does the size of the primary/base Docker change with many layers?
  - What happens when layering a Docker that contains a Bioconductor package 
    already installed in the first Docker? Is this a no-op?
  - Is there a concept of 'unlayering', e.g., removing one of the layered
    dockers?
  - After layering several dockers, try uninstalling an R package that is
    a dependency for another docker. Was the package uninstalled and does
    this break the dependent package? For example, install S4Vectors docker 
    then DelayedArray docker. Uninstall (remove or unlayer) the S4Vectors 
    docker. Is DelayedArray still functional?

* Run through 'Common Tests'.

<a name="CommonTests"></a>
### Common Tests

1. Incompatible R or Bioconductor package versions:

  Install SummarizedExperiment version 1.10.z used with R >=3.5.0. Next 
  install S4Vectors version 0.16.z used with R >=3.4.0.

  - Is S4Vectors 0.18.z replaced with 0.16.z? 
  - What is the version of R in the Docker or environment after installing
  S4Vectors 0.16.z?

2. System dependencies

Install the following packages, load them in an R session and try an example:

  - ensemblVEP: `example("ensemblVEP")`.
  - xps: `example("bgcorrect")`.
  - rsbml: `example("rsbml_problems")`.
  - ChemmineOB: `example("smartsSearch_OB")`.
  - LoMACA: `example("lfm")`.
  - GeneGA: `example("GeneGA")`.

3. Use `BiocManager` or `BiocInstaller` to install a Bioconductor package in 
  the Conda environment or BioContainer that is not available in the
  `bioconda` channel.
 
4. Identify which Bioconductor packages are not available in `bioconda`.
  If possible, find out why these aren't available and confirm
  they aren't dependencies for any Bioconductor package in `bioconda`.
