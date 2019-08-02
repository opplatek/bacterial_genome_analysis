# Workflow for methyl-directed enrichment sequencing analysis of bacterial genomes
This repository contains all the steps necessary to reproduce the analyses done in Grillova, L., Oppelt, J., & Smajs, D. (2019) [**Directly Sequenced Genomes of Contemporary Strains of Syphilis Reveal Recombination-Driven Diversity in Genes Encoding Predicted Surface-Exposed Antigens**](https://doi.org/10.3389/fmicb.2019.01691), Frontiers in Microbiology publication. 

This workflow can be used for the analysis of **any** bacterial genome and is specially designed for analysis of highly contaminated deep sequencing samples of Treponema pallidum (low-variable genome, Illumina, paired-end, Nextera). It can work with samples contaminated by a host genome DNA as well as any other organisms (both known and unknown). It does not depend on a predefined database of the contaminants and therefore avoids missing unknown organisms contamination. It is highly sensitive and specific at the same time and combines alignment-base assembly and the *de novo* assembly to cover all possible variation.

The workflow is written as [Jupyter notebook](https://jupyter.org/) and [Conda environment](https://conda.io/docs/) to ensure the reproducibility and easiness of the installation.

# Setting up the environment

## Jupyter notebook installation
If you have admin rights, you can install the [Jupyter notebook](https://jupyter.org/) with [bash kernel](https://pypi.org/project/bash_kernel/) (bash kernel works only with Python 3 version of the Jupyter notebook).

We have to figure out what version of Python is your default.

```
which python3
python3 --version
which pip
pip --version
```

In case you see Python and pip 3.x.x are your default please continue with

```
pip install --upgrade pip
pip install bash_kernel
python3 -m bash_kernel.install
```

In case pip 2.x.x is your default please continue with

```
pip3 install --upgrade pip
pip3 install bash_kernel
python3 -m bash_kernel.install
```

If you don't have the admin access, please ask your admin to install this for you. If you have access to Jupyter notebook but you cannot install the Bash kernel [this](https://blog.dominodatalab.com/lesser-known-ways-of-using-notebooks/) could be the way around.

If we would like to check the Jupyter notebook you can type:

```
jupyter notebook
```

If this fails you have to install the Jupyter notebook (please note you will need Jupyter based on Python 3 to work with bash kernel but the Conda environment we will use is on Python 2).

```
python3 -m pip install --upgrade pip
python3 -m pip install jupyter
```

## Conda installation
We strongly recommend using the provided Conda environment to ensure the compatibility and to simplify the installation. 

If you do not have Conda already installed in your system, first you have to install Conda itself. The example of the installation for 64-bit Linux is the following:

```
install_dir="/home/jan/Tools"
mkdir $install_dir
cd $install_dir

wget https://repo.anaconda.com/miniconda/Miniconda2-latest-Linux-x86_64.sh
bash Miniconda2-latest-Linux-x86_64.sh
```

and follow the instructions. 

You can also install the full Anaconda if you wish. 

```
wget https://repo.anaconda.com/archive/Anaconda3-2019.03-Linux-x86_64.sh
bash Anaconda3-2019.03-Linux-x86_64.sh
```

For more details about the Conda installation, please use the Conda help [here](https://conda.io/docs/user-guide/install/index.html) or Miniconda help [here](https://docs.conda.io/en/latest/miniconda.html).

Note: If you selected 

```
Do you wish the installer to initialize Miniconda3
by running conda init? [yes|no]
[no] >>> yes
```

you can turn it off later by executing

```
conda config --set auto_activate_base false
```

Once you installed the Conda environment, you can install the required tools and dependencies. Most of the tools can be installed directly with the provided YAML configuration. To install the environment, you can type:

```
conda env create -f treponema_conda_env.yml
```

In case you get an error

```
Collecting package metadata: done
Solving environment: failed

ResolvePackageNotFound:
  - r-pillar=1.3.1
```
or
```
Collecting package metadata: done
Solving environment: failed

UnsatisfiableError: The following specifications were found to be in conflict:
  - bioconductor-genomeinfodbdata==1.1.0=r351_0
  - dbus==1.13.0=h3a4f0e9_0
```

Please open the YAML file and delete the specific version of the package and try again. You might have to repeat this a few times. Conda is robust, but not that much. 

If you prefer, you can also install everything on your own. However, the full **compatibility cannot be ensured**. If you installed the environment using the previous command, please skip to *Finishing setting up the environment* section. The full list of used tools and dependencies can be found in the YAML file (treponema_conda_env.yml) or in the "text" format (treponema_conda_env.txt). 

```
# Create new conda environment
conda create --name treponema python=2.7
# Activate the environment and cofigure the channels
source activate treponema
conda config --add channels r
conda config --add channels conda-forge
conda config --add channels bioconda
# Install all the required tools using Conda
#conda install -c anaconda virtualenv=16.0.0 # Added to the main installation command but untested if it works
conda install -c bioconda fastqc=0.11.8 reaper=16.098 multiqc=1.3 cutadapt=1.18 bbmap=38.22 samtools=1.4 seqtk=1.3 R=3.5.1 bwa=0.7.15 picard=2.9.2 gatk=3.7 qualimap=2.2.2b bcftools=1.4 vcftools=0.1.15 perl-vcftools-vcf=0.1.15 vcflib=1.0.0_rc2 freebayes=0.9.21 spades=3.13.0 quast=5.0.2 besst=2.2.8 busco=3.0.2 blast=2.9.0 snpeff=4.3.1t kraken2=2.0.8_beta prokka=1.13.7 mummer=3.23 networkx=1.9 virtualenv=16.0.0
conda install -c conda-forge gnuplot=5.2.7
```

You can also add some Quast addition datasets 

```
quast-download-gridss
quast-download-silva
quast-download-busco
```

We have to manually copy the GATK file (because of the licencing issues)

```
gatk-register 
```

and follow the instructions.


We can check the installed environment and export it for further references

```
conda list -n treponema # Make sure you have all the packages installed
# conda env export > treponema_conda_env.yml # In case you want to export the environment for "easier" installation
```

Now navigate to the downloaded Jupyter notebook from this repository, open it and follow the rest of the instructions.

Now everything (almost - we will have to install two more tools not available through Conda) should be ready and we can launch the Jupyter notebook. 

## Finishing setting up the environment
Two tools ([StrainSeeker](http://bioinfo.ut.ee/strainseeker/) and [jvarkit](https://github.com/lindenb/jvarkit)) are not available in Conda so we have to install them separately. First, we load the Conda environment to ensure the best compatibility. One tool ([NGSutils/BAMutils](https://github.com/ngsutils/ngsutils) has a bug in the Conda installation so we have to install it manually.

```
source activate treponema
```

Now, we can download and install the tools. 

```
# Install the rest of the tools which are not available through conda
install_dir="/home/jan/Tools"
mkdir $install_dir

# StrainSeeker and it's database
cd $install_dir/
mkdir strainseeker
cd strainseeker/
wget http://bioinfo.ut.ee/strainseeker/downloads/seeker.pl
wget bioinfo.ut.ee/strainseeker/downloads/builder.pl
wget http://bioinfo.ut.ee/strainseeker/downloads/ss_helper_scripts.tar.gz
tar xvzf ss_helper_scripts.tar.gz
rm ss_helper_scripts.tar.gz
```

You will most likely need to edit the StrainSeeker executables and add `#!/usr/bin/perl` to their first row. Open `seeker.pl` and `builder.pl` and put `#!/usr/bin/perl` as their first row. 

```
# Link the executables to the Conda environment
chmod a+x $install_dir/strainseeker/*
for i in $install_dir/strainseeker/*; do ln -s $i $CONDA_PREFIX/bin/$(basename $i);done
```

Now you should be all set and the StrainSeeker should be ready to use.

```
# Jvarkit - BAM downsample
cd $install_dir
git clone "https://github.com/lindenb/jvarkit.git" # We would like to have at least commit ec2c236 (26 Feb 2019)
cd jvarkit
./gradlew biostar154220
./gradlew sortsamrefname
# ./gradlew samjs # Optional
# Link the executables to the Conda environment if necessary
chmod a+x $install_dir/jvarkit/dist/*.jar
ln -s $install_dir/jvarkit/dist/biostar154220.jar $CONDA_PREFIX/bin/downsamplebam.jar
ln -s $install_dir/jvarkit/dist/sortsamrefname.jar $CONDA_PREFIX/bin/sortsamrefname.jar
ln -s $install_dir/jvarkit/dist/samjs.jar $CONDA_PREFIX/bin/samjs.jar

# NGSutils - BAMutils
cd $install_dir/
# IMPORTANT: You need at least commit a7f08f5 (9 Feb 2017)
git clone git://github.com/ngsutils/ngsutils.git
cd ngsutils
# IMPORTANT: Make sure that both init.sh and test.sh have Python set to Python2.6+! If necessary, manualy set the Python version. For example, in init.sh (and test.sh) change "python" to "python2" otherwise you might get an error during the "make" command.
# IMPORTANT2: You might have to exit the environment to install the NGSutils
make
chmod a+x $install_dir/ngsutils/bin/*
for i in $install_dir/ngsutils/bin/*;do ln -s $i $CONDA_PREFIX/bin/$(basename $i); done
```
 
Now we have everything set, we can deactivate the environment, launch the Jupyter notebook and start with the analysis.

```
# Deactivate the environment
source deactivate
# Open the Jupyter notebook
jupyter notebook
```

And load the downloaded Jupyter notebook (**bacterial_genome_analysis.ipynb**)(https://github.com/opplatek/bacterial_genome_analysis/blob/master/bacterial_genome_analysis.ipynb) and enjoy the analysis.
