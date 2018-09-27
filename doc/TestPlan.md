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

	Set username and password for quay.io account, this must be done to
	start pushing and pulling containers from `quay.io`.

	In the command line:

	(Enter username and password, you should get a login successful notice.)

		docker login quay.io

		docker pull quay.io/biocontainers/bioconductor-deseq2:1.18.1--r3.4.1_1

	NOTE: All bioconductor packages start with
    `bioconductor-<package_name>`. The tag set is important. Quay.io
    does not pull the image without a tag. In the example above, the
    tag is `1.18.1--r3.4.1_1`.


* Is R already installed in the individual Dockers containing Bioconductor 
  packages? 

	Yes, R is installed from the conda-forge R-recipe

* Is Conda installed by default in these BioContainers and if yes, what
  channels is it pointing to?

	No, conda is not installed on the Biocontainers.

	R-version on container:

		R version 3.4.1 (2017-06-30)
		Platform: x86_64-pc-linux-gnu (64-bit)
		Running under: Buildroot 2014.02

* How does layering work?
  
  - Does the size of the primary/base Docker change with many layers?
  
  This was hard to figure out. It seems like there is no real way 
  in docker to
  
		docker image A + docker image B = Docker image AB
  
  It seems like we have to reverse engineer the image AB from the
  specifications of images A and B.
  
  The dockerfile for each biocontainer-<package_name> seems somehow
  not reproducible by parsing as well. We can get the dockerfile, by
  using the following command

		docker history --no-trunc=true 5e6aa1b01444 > deseq2-dockerfile
  
  This produces the following, 
  
```
IMAGE                                                                     CREATED             CREATED BY                                                                                                                    SIZE                COMMENT
sha256:5e6aa1b0144443d61a04f36cc958a8859c47034f6c9bede26bacc28dabbc1c17   7 months ago        /bin/sh                                                                                                                       669MB               
<missing>                                                                 2 years ago         /bin/sh -c #(nop) CMD ["/bin/sh" "-c" "/bin/bash"]                                                                            0B                  
<missing>                                                                 2 years ago         /bin/sh -c #(nop) ADD file:8583f81843640f66efa0cce8dc4f49fd769ed485caa0678ef455aa65876b03e2 in /bin/bash                      1.94MB              
<missing>                                                                 2 years ago         /bin/sh -c #(nop) MAINTAINER Bjoern Gruening <bjoern.gruening@gmail.com>                                                      0B                  
<missing>                                                                 2 years ago         /bin/sh -c #(nop) CMD ["/bin/sh"]                                                                                             0B                  
<missing>                                                                 2 years ago         /bin/sh -c opkg-cl install http://downloads.openwrt.org/snapshots/trunk/x86/64/packages/base/libc_1.1.10-1_x86_64.ipk         441kB               
<missing>                                                                 2 years ago         /bin/sh -c opkg-cl install http://downloads.openwrt.org/snapshots/trunk/x86/64/packages/base/libgcc_4.8-linaro-1_x86_64.ipk   70.6kB              
<missing>                                                                 2 years ago         /bin/sh -c #(nop) ADD file:d01ddbb13c1e847e7064ac35cbdc5c840c3a64c9aef2552aec5315a5375da312 in /lib/functions.sh              9.15kB              
<missing>                                                                 2 years ago         /bin/sh -c #(nop) ADD file:e2c3819e14cb4b8d2fff4ba6b2e2f49992788470d18d18f4aba3f3aaf2b30d40 in /usr/bin/opkg-install          103B                
<missing>                                                                 2 years ago         /bin/sh -c #(nop) ADD file:1fb1c8c23666e2dc3a1cfe388868f16a3b46cbfa5b2dfd5b43382adfd1599527 in /etc/opkg.conf                 220B                
<missing>                                                                 2 years ago         /bin/sh -c #(nop) ADD file:317a8c7f54c369601633fa49b1820a28778446f3a253eba0db0ef3fdb71461a4 in /                              4.27MB              
<missing>                                                                 2 years ago         /bin/sh -c #(nop) MAINTAINER Jeff Lindsay <progrium@gmail.com>                                                                0B                  
```
  
  The values which include (nop) or ADD are not possible to replicate,
  as they are coming from 
  
  
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
