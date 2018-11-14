Using Bioconda for Bioconductor package management
=================================================

## Goal 

The goal of this document is to give a brief outline of how to
use [Bioconda][] for Bioconductor package management and installation. 

Bioconductor recommends using [BiocManager][] and installing R via
instructions given on this page (http://bioconductor.org/install/).

This document aims to support a few users who have chosen to use the
conda package manager. It is not a replacement in any way for the
canonical method of using Bioconductor(given [Install Bioconductor][]).

## How to use Bioconductor with Bioconda

Conda is a package manager which allows for installing packages in
different languages on a conda environement within the users machine.

"Bioconda is a channel for the conda package manager specializing in
bioinformatics software. Bioconda consists of: a repository of recipes
hosted on GitHub. a build system that turns these recipes into conda
packages." (taken from [Bioconda][])

NOTE: This document assumes that the user is familiar with `conda` and
chooses to use `conda` as the package manager over Bioconductor's
default installation method using [BiocManager][] within R. 

Bioconda now supports all the packages in *Bioconductor version 3.8*,
with *R-3.5.1*.

To use Bioconda, make sure you have the channels set appropriately, to
set channels, the following command adds the channel “new_channel” to
the top of the channel list, making it the highest priority:

	conda config --add channels <new_channel>

So for the correct configuration of channels required for
Bioconductor, the user should have

	conda config --add channels defaults
	conda config --add channels conda-forge
	conda config --add channels bioconda

To make sure that this worked the best way to check is looking at the
`conda config`.

	conda config --show | grep -A 3 "^channels:"

	channels:
		- bioconda
		- conda-forge
		- defaults

More information: [Managing Conda Channels][]


Following this, the installation of packages in a conda environment
is straight forward. 

1. To create a new conda envinronment, 

		conda create --name <my_environment>
	
		conda activate <my_environment>
	
1. To install required bioconductor packages,

		conda install bioconductor-biocparallel
	

    **NOTE:**	
	
	When using `conda` for installing of Bioconductor packages, it is
    important that the user always uses `conda install`. There should
    be no switching back and forth using `BiocManager`. This is
    because the R packages need to be installed in the correct
    R-library.
	
	Conda installs R-3.5.1 as the default R version. The packages
    available via conda for Bioconductor are only going to be the ones
    in the lastest RELEASE_X_Y. 
	
	Bioconductor `devel` packages are not available at the
    moment. Neither is R-devel.
	

1. To check the list of Bioconductor packages available through your
   conda environment, you can use,
   
		conda list | grep 'bioconductor'
		
   To check the packages within R-3.5.1 installed via bioconda,
   make sure you start up the correct R version. It should be located
   in the `miniconda` directory. My bioconda installation of R, is
   located in an environment called `demo`.
   
   R executable, 
   
	    ~/miniconda/envs/demo/bin/R
   
   R library, where all my packages are installed through conda
   
		~/miniconda/envs/demo/lib/R/library
		

## Basic Workflow with references to Bioconda

NOTE: As a starting point, (We assume) you have miniconda installed on
      your machine. Without it interfering with your base R
      environment. If not, please take a look at [Miniconda Installation][]


1. Start your `conda` environement where you want to install your package,

		conda activate <bioconductor-environment>
	
1. Install packages you need,

		conda install <bioconductor-package_name> 
		
	NOTE: All bioconductor packages in [Bioconda][] are written with
    prefix `bioconductor-` and suffix is a all lowercase Bioconductor
    package name irrespective of what is on Bioconductor. So a package
    like `BiocParallel` on Bioconductor, would be
    `bioconductor-biocparallel`, on [List of available packages][] on
    [Bioconda][]

1.  Start your R session as installed by [Bioconda][],

		~/miniconda/envs/demo/bin/R
		
	This should start your R,

		R version 3.5.1 (2018-07-02) -- "Feather Spray"
		Copyright (C) 2018 The R Foundation for Statistical Computing
		Platform: x86_64-apple-darwin14.5.0 (64-bit)

		R is free software and comes with ABSOLUTELY NO WARRANTY.
		You are welcome to redistribute it under certain conditions.
		Type 'license()' or 'licence()' for distribution details.

		  Natural language support but running in an English locale

		R is a collaborative project with many contributors.
		Type 'contributors()' for more information and
		'citation()' on how to cite R or R packages in publications.

		Type 'demo()' for some demos, 'help()' for on-line help, or
		'help.start()' for an HTML browser interface to help.
		Type 'q()' to quit R.

		> sessionInfo()
		R version 3.5.1 (2018-07-02)
		Platform: x86_64-apple-darwin14.5.0 (64-bit)
		Running under: macOS Sierra 10.12.6

		Matrix products: default
		BLAS: /System/Library/Frameworks/Accelerate.framework/Versions/A/Frameworks/vecLib.framework/Versions/A/libBLAS.dylib
		LAPACK: /System/Library/Frameworks/Accelerate.framework/Versions/A/Frameworks/vecLib.framework/Versions/A/libLAPACK.dylib

		locale:
		[1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8

		attached base packages:
		[1] stats     graphics  grDevices utils     datasets  methods   base

		loaded via a namespace (and not attached):
		[1] compiler_3.5.1


## Support contact

Documentation is available on the [Bioconda homepage][] is
very useful 

The [Gitter channel][] for Bioconda support is also extremely useful
for people have issues. Gitter provides a online community for
Bioconda, where users can interact with the Bioconda core members via
chat.

The [Bioconda FAQ][] section is another good resource for any
questions you may have.


### (Optional) Installing Conda on a Mac / Linux

Goal: Installing conda on a mac without overriding my system python or
R.

Download miniconda https://conda.io/miniconda.html,
`Miniconda3-latest-MacOSX-x86_64.sh`.

CMD:

	bash ~/Miniconda3-latest-MacOSX-x86_64.sh -b -p $HOME/miniconda

To run the silent installation of Miniconda for macOS or Linux,
specify the -b and -p arguments of the bash installer. The following
arguments are supported:

	-b  Batch mode with no PATH modifications to ~/.bashrc. Assumes that
		you agree to the license agreement. Does not edit the .bashrc or
		.bash_profile files. 
	-p  Installation prefix/path.
	

In each new bash session, before using conda, set the PATH and run the
activation scripts of your conda packages by running:

This starts the `base` conda-env

	source $HOME/miniconda/bin/activate

NOTE: Default version of python is 3.6


### Important links


[Bioconda](https://bioconda.github.io/index.html)

[BiocManager](https://cran.r-project.org/web/packages/BiocManager/index.html)

[List of Avialable packages](https://bioconda.github.io/recipes.html)

[Bioconda FAQ](https://bioconda.github.io/faqs.html)

[Managing Conda Channels](https://conda.io/docs/user-guide/tasks/manage-channels.html)

[Gitter channel](https://gitter.im/bioconda/Lobby)

[Miniconda Installtion](https://conda.io/miniconda.html)




[Bioconda]: https://bioconda.github.io/index.html

[Bioconda homepage]: https://bioconda.github.io/index.html

[BiocManager]: https://cran.r-project.org/web/packages/BiocManager/index.html

[List of Avialable packages]: https://bioconda.github.io/recipes.html

[Bioconda FAQ]: https://bioconda.github.io/faqs.html

[Managing Conda Channels]: https://conda.io/docs/user-guide/tasks/manage-channels.html

[Gitter channel]: https://gitter.im/bioconda/Lobby

[Miniconda Installtion]: https://conda.io/miniconda.html

[Install Bioconductor]: http://bioconductor.org/install/
