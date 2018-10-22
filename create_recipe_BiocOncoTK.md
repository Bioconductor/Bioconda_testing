# Create a recipe for BiocOncoTK


## Get bioconda-recipes

1. Fork https://github.com/biocond/bioconda-recipes.git to
   https://github.com/nturaga/bioconda-recipes.git. 
   
1. Clone the forked repo.

		git clone git@github.com:nturaga/bioconda-recipes.git

1. Make new branch 

		git checkout -b biconductor-BiocOncoTK
		

## Test locally

https://bioconda.github.io/contribute-a-recipe.html#bootstrap

Method: setting up a separate Miniconda installation and running bioconda-utils. 

1. Get installtion of miniconda in `/tmp` dir. 

		sh Miniconda3-latest-MacOSX-x86_64.sh
		
		options: 
		
			Location: /tmp/miniconda
			
			add to bash: no
			
1. Do the bootstrap method as given in documentation 

		cd Documents/bioconda-recipes

		./bootstrap.py /tmp/miniconda
		
		source ~/.config/bioconda/activate


The last command activates the `base` environment for conda with,
 
	python 3.6

	bioconda-utils


1. Lint all new recipes, if any new ones added.

		bioconda-utils lint recipes config.yml --git-range master

		12:33:47 BIOCONDA INFO Recipes newly unblacklisted:
	
		12:33:47 BIOCONDA INFO No recipe modified according to git, exiting.


1. In the `bioconda-recipes` directory, there is a `config.yml`

		cat bioconda-recipes/config.yml

This results,

```
env_matrix: "scripts/env_matrix.yml"

blacklists:
    - "build-fail-blacklist"

# Channels ordered from highest to lowest priority. This should match the order
# of .condarc, but when adding channels using conda config --add, they should
# be added in reverse (from low to high priority).
channels:
    - conda-forge
    - bioconda
    - defaults
```

1. Create new recipe as a test for BiocOncoTK,

		cd bioconda-recipes

	CMD:

		bioconda-utils bioconductor-skeleton recipes config.yml BiocOncoTK
		
		
This results in
		
```		
12:39:05 BIOCONDA INFO Making recipe for: BiocOncoTK
12:39:05 BIOCONDA DEBUG BiocOncoTK==1.0.3, BioC==3.7
12:39:15 BIOCONDA INFO Using tarball from http://bioconductor.org/packages/3.7/bioc/src/contrib/BiocOncoTK_1.0.3.tar.gz
12:39:15 BIOCONDA INFO creating recipes/bioconductor-bioconcotk
12:39:18 BIOCONDA INFO Downloading http://bioconductor.org/packages/3.7/bioc/src/contrib/BiocOncoTK_1.0.3.tar.gz to /var/folders/g2/j0ltphps6_90_9dhhv7m77xr0000gp/T/cached_bioconductor_tarballs/BiocOncoTK_1.0.3.tar.gz
12:39:32 BIOCONDA INFO BioConductor dependency: name="ComplexHeatmap" version=""
12:39:35 BIOCONDA INFO            R dependency: name="DT" version=""
12:39:38 BIOCONDA INFO            R dependency: name="R" version=">=3.5.0"
12:39:39 BIOCONDA INFO BioConductor dependency: name="S4Vectors" version=""
12:39:40 BIOCONDA INFO            R dependency: name="bigrquery" version=""
12:39:40 BIOCONDA INFO            R dependency: name="dplyr" version=""
12:39:41 BIOCONDA INFO            R dependency: name="httr" version=""
12:39:41 BIOCONDA INFO            R dependency: name="magrittr" version=""
12:39:42 BIOCONDA INFO            R dependency: name="rjson" version=""
12:39:42 BIOCONDA INFO            R dependency: name="shiny" version=""
12:39:42 BIOCONDA INFO Wrote recipe in recipes/bioconductor-bioconcotk
```

There is a new recipe now, in 

	/Users/ni41435_ca/Documents/bioconda-recipes/recipes/bioconductor-bioconcotk

1. build and test, **WITHOUT** docker

		bioconda-utils build recipes config.yml --git-range master


	**WITH** docker

		bioconda-utils build recipes config.yml --docker --mulled-test --git-range master



