# Grillova et al. DpnI enrichment 
This repository contains all the steps necessary to reproduce the analyses done in Grillova et al. 2019 - DpnI enrichment publication. 

This workflow can be used for analysis of **any** bacterial genome and is especially designed for analysis of highly contaminated deep sequencing samples of Treponema (Illumina, paired-end, Nextera). It can work with samples contaminated by a host genome DNAas well as any other organisms (both known and unknown). It does not depend on predefined database of the contaminants and therefore avoids missing unknown organisms contamnation. It is highly sensitive and specific at the same time and combines alignment-base assembly and the *de novo* assembly to cover all possible variation.

The workflow is written as [Jupyter notebook](https://jupyter.org/) and [Conda environment](https://conda.io/docs/) to ensure the reproducibility and easiness of the installation.

# Setting up the environment

## Jupyter notebook installation
First, we can install the [Jupyter notebook](https://jupyter.org/) with [bash kernel](https://pypi.org/project/bash_kernel/) (bash kernel works only with Python 3 version of the Jupyter notebook).

```
$ which pip3
$ pip3 --version
$ which python3
$ python3 --version

$ pip3 install --upgrade pip
$ pip3 install bash_kernel
$ python3 -m bash_kernel.install
```

If we would like to start the Jupyter notebook we could type (but don't do now):

```
$ jupyter notebook
```

## Conda installation
We strongly recommend to use the provided Conda environment to ensure the compatibility and to simplify the installation. 

If you do not have Conda already installed in your system, first you have to install Conda itself. The example of the installation for 64-bit Linux is the following:

```
$ install_dir="/home/jan/Tools"
$ mkdir $install_dir
$ cd $install_dir

$ wget https://repo.continuum.io/archive/Anaconda2-2018.12-Linux-x86_64.sh
$ bash Anaconda2-2018.12-Linux-x86_64.sh
```

For more details about the Conda installation please use the Conda help [here](https://conda.io/docs/user-guide/install/index.html).

Once you installed the Conda environment you can install the required tools and dependencies. Most of the tools can be installed directly with the provided YAML configuration. To install the environemnt you can type:

```
$ conda env create -f treponema_conda_env.yml
```

If you prefere, you can also install everything on your own. However, the full **compatibility cannot be ensured**. If you installed the environment using the previous command please skip to *Finishing setting up the environment* section. The full list of used tools and dependencies can be found in the YAML file (treponema_conda_env.yml) or in the "text" format (treponema_conda_env.txt). 

```
$ # Create new conda environment
$ conda create --name treponema python=2.7
$ # Activate the environment and cofigure the channels
$ source activate treponema
$ conda config --add channels r
$ conda config --add channels conda-forge
$ conda config --add channels bioconda
$ # Install all the required tools using Conda
$ conda install -c bioconda fastqc=0.11.8 reaper=16.098 multiqc=1.7 cutadapt=1.18 bbmap=38.22 samtools=1.4 seqtk=1.3 R=3.5.1 bwa=0.7.15 picard=2.9.2 gatk=3.7 qualimap=2.2.2b bcftools=1.4 vcftools=0.1.15 vcflib=1.0.0_rc2 $ freebayes=0.9.21 spades=3.13.0 quast=5.0.2 besst=2.2.8 busco=3.0.2 blast=2.7.1 snpeff=4.3.1t # ngsutils=0.5.9
$ conda install -c anaconda virtualenv=16.0.0
```


Now navigate to the downloaded Jupyter notebook from this repository, open open it and follow the rest of the instructions.

Now everything (almost - we will have to install two more tools not available through Conda) should be ready and we can launch the Jupyter notebook. 

## Finishing setting up the environment
Two tools ([jvarkit](https://github.com/lindenb/jvarkit) and [StrainSeeker](http://bioinfo.ut.ee/strainseeker/)) are not available in Conda so we have to install them separately. First we load the Conda environment to ensure the best compatibility. One tool ([NGSutils/BAMutils](https://github.com/ngsutils/ngsutils) has a bug in the Conda installation so we have to install it manually.

```
$ source activate treponema
```

Now, we can download and install the tools. 

```
$ # Install the rest of the tools which are not available through conda
$ install_dir="/home/jan/Tools"
$ mkdir $install_dir

$ # StrainSeeker and it's database
$ cd $install_dir/
$ mkdir strainseeker
$ cd strainseeker/
$ wget http://bioinfo.ut.ee/strainseeker/downloads/seeker.pl
$ wget bioinfo.ut.ee/strainseeker/downloads/builder.pl
$ wget http://bioinfo.ut.ee/strainseeker/executables/ss_db_w32_4324.tar.gz
$ wget http://bioinfo.ut.ee/strainseeker/downloads/ss_helper_scripts.tar.gz
$ tar xvzf ss_helper_scripts.tar.gz
$ # Link the executables to the Conda environment if necessary
$ ln -s $install_dir/strainseeker/seeker.pl $CONDA_PREFIX/bin/seeker.pl
$ ln -s $install_dir/strainseeker/builder.pl $CONDA_PREFIX/bin/builder.pl

$ # Jvarkit - BAM downsample
$ cd $install_dir
$ git clone "https://github.com/lindenb/jvarkit.git" # We would like to have at least commit ec2c236
$ cd jvarkit
$ ./gradlew biostar154220
$ ./gradlew sortsamrefname
$ # Link the executables to the Conda environment if necessary
$ ln -s $install_dir/jvarkit/dist/biostar154220.jar $CONDA_PREFIX/bin/downsamplebam.jar
$ ln -s $install_dir/jvarkit/dist/biostar154220.jar $CONDA_PREFIX/bin/biostar154220.jar # Just for compatibility reasons
$ ln -s $install_dir/jvarkit/dist/sortsamrefname.jar $CONDA_PREFIX/bin/sortsamrefname.jar

$ # NGSutils - BAMutils
$ cd $install_dir/
$ source activate treponema
$ # IMPORTANT: You need at least commit a7f08f5 (9 Feb 2017)
$ git clone git://github.com/ngsutils/ngsutils.git
$ cd ngsutils
$ # IMPORTANT: Make sure that both init.sh and test.sh have Python set to Python2.6+! If necessary, manually rename the Python version. For example, in init.sh (and test.sh) change "python" to "python2" otherwise you might get an error during the "make" command.
$ # IMPORTANT2: You might have to exit the environment to install the NGSutils
$ make
$ for i in $install_dir/ngsutils/bin/*;do ln -s $i $CONDA_PREFIX/bin/$(basename $i); done
```
 
Now we have everything set, we can deactivate the environment, launch the Jupyter notebook and start with the analysis.

```
$ # Deactivate the environment
$ source deactivate
$ # Open the Jupyter notebook
$ jupyter notebook
```
