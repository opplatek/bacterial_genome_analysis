# Grillova et al. DpnI enrichment 
This repository contains all the steps necessary to reproduce the analyses done in Grillova et al. 2019 - DpnI enrichment publication. 

This workflow can be used for analysis of **any** bacterial genome and is specially designed for analysis of highly contaminated deep sequencing samples. It can work with contamination by the host sample as well as any other organism (both known and unknown). It does not depend on predefined database of the contaminants and avoids missing unknown organisms contamnation. It is highly sentistive and specific at the same time and combines alignment-base assembly and *de novo* assembly.

The workflow is available built in [Conda](https://conda.io/docs/) with [Jupyter notebook](https://jupyter.org/) to allow easy installation and reproducibility.

# Setting up the environment

## Conda installation
We strongly recommend to use the provided Conda environment to ensure the compatibility. 

If you do not have Conda already installed in your system, first have to install Conda itself. The example of the installation for 64-bit Linux is the following:

```
$ wget https://repo.continuum.io/archive/Anaconda2-2018.12-Linux-x86_64.sh
$ bash Anaconda2-2018.12-Linux-x86_64.sh
```

For more details about the Conda installation please use the Conda help [here](https://conda.io/docs/user-guide/install/index.html).

Once you installed the Conda environment you can install the required tools. Most of the tools can be installed directly as a Conda environment. We can install them using the provided YAML configuration file.

```
$ conda env create -f environment.yml
```

If everything went well we can activate the installed environment and open the Jupyter notebook.

```
$ source activate treponema
$ jupyter notebook
```

From the Jupyter notebook we can open the provided Jupyter notebook and follow the steps of the analysis.
