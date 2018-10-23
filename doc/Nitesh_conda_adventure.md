Installing Conda on Mac
=======================

## Step 1

**Goal**: Installing conda on a mac without overriding my system python or R.

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
	-f —Force (DON'T WANT THIS) installation even if prefix -p already exists.


HACK:

I saved the following command in a new script, called `activate_conda`

	export PATH="$HOME/miniconda/bin:$PATH"


In each new bash session, before using conda, set the PATH and run the
activation scripts of your conda packages by running:

This starts the `base` conda-env

	source $HOME/miniconda/bin/activate


The activate script looks like, 

	#!/bin/sh
	_CONDA_ROOT="/Users/ni41435_ca/miniconda"
	\. "$_CONDA_ROOT/etc/profile.d/conda.sh" || return $?
	_conda_activate "$@"


NOTE: Default version of python is 3.6

## Step 2

**Goal**: Test installing various python packages on a new test conda env

Create env

	conda create --name test

Install tensorflow,

	conda activate test
	
	conda install tensorflow jupyter matplotlib
	

list packages in env

	conda list -n test 


## Step 3:

**Goal**: Test installing R and configuring channels.

Set channels, the following command adds the channel “new_channel” to
the top of the channel list, making it the highest priority:

	conda config --add channels 

Conda also now has a command that adds the new channel to the bottom
of the channel list, making it the lowest priority:

	conda config --append channels new_channel


Based on Val's notes, it seems like `bioconda` is being given
preference since it was added last.

      conda config --add channels defaults
      conda config --add channels conda-forge
      conda config --add channels bioconda


## Step 4:

Test out Biocontainers.

1. Have docker installed on my local machine.

1. Have a real tag number,

		docker pull quay.io/biocontainers/bioconductor-deseq2:1.18.1--r3.4.1_1

1. Run docker biocontainer, 
   
   
   Run bash
   
		docker run -ti <image_id> /bin/bash

   Run R,

		docker run -ti quay.io/biocontainers/bioconductor-deseq2 R

1. It's important to pass in files which are available on the local
   machine to be accessible to the docker container.
   
		docker run -ti -v <system_path>:<image_path> <image_id> /bin/bash

	example:
   
		docker run -v /tmp/KEGGlincs/data:/data -ti 5e6aa1b01444 /bin/bash

Documentation on storage volumes: https://docs.docker.com/storage/volumes/


