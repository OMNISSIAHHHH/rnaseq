# RNASEQ
This repository is used for project RNA sequencing for Institute of Biophysics of Chinese Academy of Science (IBP)
## SETUP
### A. Install Miniconda
MACOS:
```
wget https://repo.continuum.io/miniconda/Miniconda3-latest-MacOSX-x86_64.sh -O ~/miniconda.sh

Run the miniconda installation
bash miniconda.sh -b -f -p ~/miniconda

Add miniconda to the system path
echo 'PATH="$HOME/miniconda/bin:$PATH' >> ~/.bash_profile

Source system file to activate miniconda
source ~/.bash_profile

Add bioinformatic channels for downloading required packages
conda config --add channels conda-forge
conda config --add channels defaults
conda config --add channels r
conda config --add channels bioconda
```
WINDOWS:
[Official Minidonda Installation Guide](https://docs.conda.io/projects/conda/en/latest/user-guide/install/windows.html)

## Quality Analysis
## Difference Analysis


