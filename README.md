# RNASEQ
RNA sequencing for Liang's Lab, Institute of Biophysics of Chinese Academy of Science (IBP). 供中国科学院生物物理研究所梁伟组使用的转录组数据分析项目。

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
FastQC aims to provide a simple way to do some quality control checks on raw sequence data coming from high throughput sequencing pipelines.
[Introduction to FastQC output content](https://zhuanlan.zhihu.com/p/20731723)  
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
-- -o/--output_dir <DIR>： If specified all output will be written to this directory instead of the current directory.
If the directory doesn't exist it will be created for you. 【特别指定结果输出位置，如该路径不存在，则会创造该路径】
```
For more options visit [Trim Galore Official Usage Guide](https://github.com/FelixKrueger/TrimGalore/blob/master/Docs/Trim_Galore_User_Guide.md)

## Analysis Procedure 3: rRNA Removal(Conditional) 分析进程3: rRNA序列删除（条件性）

### Method: SortMeRNA
SortMeRNA is a local sequence alignment tool for filtering, mapping and OTU clustering. The core algorithm is based on approximate seeds and allows for fast and sensitive analyses of NGS reads. The main application of SortMeRNA is filtering rRNA from metatranscriptomic data. 

### SortMeRNA Installation
Installation through Bioconda:
```
conda install -c bioconda sortmerna --yes
```
### SortMeRNA Command
Usage:  
The only required options are --ref and --reads. Options (any) can be specified usig a single dash e.g. -ref and -reads. 
Both plain fasta/fastq and archived fasta.gz/fastq.gz files are accepted. File extensions .fastq, .fastq.gz, .fq, .fq.gz, .fasta, … are optional. 
The format and compression are automatically recognized. Relative paths are accepted.  

Common Commands:
```
sortmerna --ref REF_PATH_1 --ref REF_PATH_2 --ref REF_PATH_3 --reads READS_PATH_1 --reads READS_PATH_2
【如果是paired results 则须如上方所示 --reads 两个文件，如果是 single， 则仅需一个--reads。ref 文件可以有多个】
```
For More information, visit [SortMeRNA Documentation](https://sortmerna.readthedocs.io/en/latest/index.html)

## Analysis Procedure 4: Genome Alignment 分析进程4:基因组比对
### Method: STAR 
Spliced Transcripts Alignment to a Reference (STAR) is a fast RNA-seq read mapper, with support for splice-junction and fusion read detection.
STAR aligns reads by finding the Maximal Mappable Prefix (MMP) hits between reads (or read pairs) and the genome, using a Suffix Array index.
### STAR Installation
Installation through Bioconda:
```
conda install -c bioconda star -yes
```
### STAR Command
* Star_index 建立基因组索引  
在使用aligner之前需要先建立基因组索引，包含fasta序列文件以及gtf annotation文件。本repo包含有人类基因组Gh38.p14 (截止2023.8.3的最新版本），
文件名为Homo_sapien_gh38. 本步骤仅运行一次，由于运算量巨大，可能需要30min至3h左右的时间
```
STAR --runMode genomeGenerate \ 【创造索引模式开启】
--runThreadN  20 \ 【规定线程数】
--genomeFastaFiles \ 【\后输入路径提供基因组fasta文件】
--genomeDir hg19_STAR_db 【提供索引文件输出目录，该目录（文件夹）需要提前创造好。可以通过mkdir先创造，或者直接生成文件夹后复制路径】
--sjdbGTFfile hg19.gtf \ 【\后输入路径提供基因组gtf 注释文件】
--sjdbOverhang 【overhang数字，默认为100】
```
* Star_alignment 基因比对
```
STAR --runThreadN  20 \ 【规定线程数】
--genomeDir /path/to/genomeDir 【输入索引文件路径】
--readFilesIn /path/to/read1 [/path/to/read2 ] 【输入比对测序fasta文件路径，如果是paired read 则两个都需要输入.
如果有多对基因文件，则须：
For single-end reads use a comma separated list
(no spaces around commas), e.g.:
--readFilesIn sample1.fq,sample2.fq,sample3.fq

For paired-end reads, use comma separated list for read1, followed by space, followed by comma
separated list for read2, e.g.:
--readFilesIn s1read1.fq,s2read1.fq,s3read1.fq s1read2.fq,s2read2.fq,s3read2.fq

For multiple read files, the corresponding read groups can be supplied with space/comma/space-
separated list in --outSAMattrRGline, e.g.
--outSAMattrRGline ID:sample1 , ID:sample2 , ID:sample3】
```
#### possible issue 可能存在的问题  
mac m1/m2 (pro) chip: 输入指令时不要输入runThreadN。该指令会指定分配给任务的线程数。制定线程数可能会导致电脑严重卡顿，且造成自动终止 (zsh: killed)   

For more information, visit [STAR Manual](https://physiology.med.cornell.edu/faculty/skrabanek/lab/angsd/lecture_notes/STARmanual.pdf)

## Analysis Procedure 5: Gene Counting 分析进程5:基因计数

### Method: featureCounts (Subread)

### Subread Installation

Installation through Bioconda:
```
conda install -c bioconda subread - yes
```
Usage:
```
待添加
```
Output:
```
待添加
```

## Analysis Procedure 6: Fast Report via MultiQC 分析进程6:使用MultiQC生成简易结果报告
### Method: MultiQC 
We present MultiQC, a tool to create a single report visualising output from multiple tools across many samples, enabling global trends and biases to be quickly identified. MultiQC can plot data from many common bioinformatics tools and is built to allow easy extension and customization.  
### MultiQC Command
Installation through Bioconda:
```
conda install -c bioconda multiqc -yes
```
Usage
```
# Scan:
multiqc data/
multiqc data/ ../proj_one/analysis/ /tmp/results
multiqc data/*_fastqc.zip
multiqc data/sample_1*
multiqc --file-list my_file_list.txt 【如果有一个储存好的文件路径列表在txt中，也可以读取该txt文件】

# Rename:
The report is called multiqc_report.html by default. Tab-delimited data files are created in multiqc_data/, containing additional information. You can use a custom name for the report with the -n/--filename parameter, or instruct MultiQC to create them in a subdirectory using the -o/--outdir parameter.

# Exporting Plots:
You can do this with the -p/--export command line flag. By default, plots will be saved in a directory called multiqc_plots as .png, .svg and .pdf files. Raw data for the plots are also saved to files.
# Help:
multiqc -h

```

---
以下为非必要分析流程，用于获得进一步的数据可视化结果
## Analysis Procedure 7: R Data Input 分析进程7: R数据输入
## Analysis Procedure 8: Gene Symbols Annotation 分析进程8: 基因注释
## Analysis Procedure 9: Data Plot & Visualizatioin 分析进程9: 数据图表制作及数据可视化






