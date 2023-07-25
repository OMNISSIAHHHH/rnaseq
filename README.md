# RNASEQ
This repository is used for project RNA sequencing for Liang's Lab, Institute of Biophysics of Chinese Academy of Science (IBP).
## SETUP 准备
### A. Install Miniconda 
分析流程中将运用到大量的miniconda包，安装方法如下：

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
## Analysis Procedure 1: Quality Check 分析进程1: 质量检测
### Method: FastQC
FastQC aims to provide a simple way to do some quality control checks on raw sequence data coming from high throughput sequencing pipelines.[Introduction to FastQC output content](https://zhuanlan.zhihu.com/p/20731723)  
### FastQC Installation
Installation through bioconda:  
```
conda install -c bioconda fastqc --yes
```
Installation through [official website](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)  
### FastQC Command
```
fastqc -o <output directory> <sequencing file 1, sequencing file 2...>   <-- set output directory, and each sequencing file will generate a quality report.  
【在指定输出路径输出质量分析文件，每个测序文件单独生成一个质量分析文件】  
For more help on fastqc: fastqc -h  
【查看帮助文档】
```

## Analysis Procedure 2: Low Quality Sequence Removal 分析进程2: 低质量序列删除
### Method: Trim Galore  

### Trim Galore Installation  
Installation through Bioconda:
```
conda install trim-galore
```
### Trim Galore Command
```
Usage: trim_galore [options] <filename(s)>`
Common [options]
-- illumina: Adapter sequence to be trimmed is the first 13bp of the Illumina universal adapter AGATCGGAAGAGC 
instead of the default auto-detection of adapter sequence. 【指定裁剪掉illumina 特有adapter 序列，而非自动识别】
-- gzip: Compress the output file with gzip. 【使用gzip压缩输出文件】
-- -o/--output_dir <DIR>： If specified all output will be written to this directory instead of the current directory. If the directory doesn't exist it will be created for you. 【特别指定结果输出位置，如该路径不存在，则会创造该路径】
```
For more options visit [Trim Galore Official Usage Guide](https://github.com/FelixKrueger/TrimGalore/blob/master/Docs/Trim_Galore_User_Guide.md)

## Analysis Procedure 3: rRNA Removal(Conditional) 分析进程3: rRNA序列删除（条件性）
### Method: SortMeRNA

### SortMeRNA Installation
Installation through Bioconda:
```
conda install -c bioconda sortmerna --yes
```
### SortMeRNA Command
Usage:  
The only required options are --ref and --reads. Options (any) can be specified usig a single dash e.g. -ref and -reads. Both plain fasta/fastq and archived fasta.gz/fastq.gz files are accepted. File extensions .fastq, .fastq.gz, .fq, .fq.gz, .fasta, … are optional. The format and compression are automatically recognized. Relative paths are accepted.  
Common Commands:
```
sortmerna --ref REF_PATH_1 --ref REF_PATH_2 --ref REF_PATH_3 --reads READS_PATH_1 --reads READS_PATH_2
【如果是paired results 则须如上方所示 --reads 两个文件，如果是 single， 则仅需一个--reads。ref 文件可以有多个】
```
For More information, visit [SortMeRNA Documentation](https://sortmerna.readthedocs.io/en/latest/index.html)

## Analysis Procedure 4: Genome Alignment 分析进程4:基因组比对
### Method: STAR V
## Analysis Procedure 5: Gene Counting 分析进程5:基因计数

## Analysis Procedure 6: Fast Report via MultiQC 分析进程6:使用MultiQC生成简易结果报告
---
以下为非必要分析流程，用于获得进一步的数据可视化结果
## Analysis Procedure 7: R Data Input 分析进程7: R数据输入
## Analysis Procedure 8: Gene Symbols Annotation 分析进程8: 基因注释
## Analysis Procedure 9: Data Plot & Visualizatioin 分析进程9: 数据图表制作及数据可视化






