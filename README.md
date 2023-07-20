# RNASEQ
This repository is used for project RNA sequencing for Institute of Biophysics of Chinese Academy of Science (IBP)
## SETUP 准备
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
### B. Folder Structure (待修改）
```
── new_workflow/
  │   └── annotation/               <- Genome annotation file (.GTF/.GFF)
  │  
  │   └── genome/                   <- Host genome file (.FASTA)
  │  
  │   └── input/                    <- Location of input  RNAseq data
  │  
  │   └── output/                   <- Data generated during processing steps
  │       ├── 1_initial_qc/         <- Main alignment files for each sample
  │       ├── 2_trimmed_output/     <-  Log from running STAR alignment step
  │       ├── 3_rRNA/               <- STAR alignment counts output (for comparison with featureCounts)
  │           ├── aligned/          <-  Sequences that aligned to rRNA databases (rRNA contaminated)
  │           ├── filtered/         <-  Sequences with rRNA sequences removed  (rRNA-free)
  │           ├── logs/             <- logs from running SortMeRNA
  │       ├── 4_aligned_sequences/  <- Main alignment files for each sample
  │           ├── aligned_bam/      <-  Alignment files generated from STAR (.BAM)
  │           ├── aligned_logs/     <- Log from running STAR alignment step
  │       ├── 5_final_counts/       <- Summarized gene counts across all samples
  │       ├── 6_multiQC/            <- Overall report of logs for each step
  │  
  │   └── sortmerna_db/             <- Folder to store the rRNA databases for SortMeRNA
  │       ├── index/                <- indexed versions of the rRNA sequences for faster alignment
  │       ├── rRNA_databases/       <- rRNA sequences from bacteria, archea and eukaryotes
  │  
  │   └── star_index/               <-  Folder to store the indexed genome files from STAR 
```
## Analysis Procedure 分析进程


