# single-cell variant calling
Variant calling for single cell DNA sequencing

## Overview
In this project I am testing a strategy for variant calling in single cell DNA sequencing data. I am using data generated in ["Factors influencing meiotic recombination revealed by whole-genome sequencing of single sperm" - Hinch et al., Science 2019](https://science.sciencemag.org/content/363/6433/eaau8861/). The dataset includes short-read DNA sequencing data from bulk (many cells) and single sperm cells. In a fist step, Google Deep Variant, a deep learning algorithm, is used for *de novo* variant calling in a bulk sample (["A universal SNP and small-indel variant caller using deep neural networks" - Poplin et al., Nature Biotechnology 2018](https://www.nature.com/articles/nbt.4235)). In a second step ProSolo, a probabilistic model which jointly models single and bulk sequencing samples is used to call variants in signle cells (["ProSolo: Accurate Variant Calling from Single Cell DNA Sequencing Data" - Lähnemann et al., bioRxiv 2020](https://www.biorxiv.org/content/10.1101/2020.04.27.064071v1)). 

### Data Download
FASTQ files were downloaded from the [European Nucleotide Archive (ENA)](https://www.ebi.ac.uk/ena/browser/home) 

### Reference Genome
The lates version of the mouse genome mm39 was used as a reference. The FASTA file was downloaded from [UCSC Genome Browser](https://hgdownload.soe.ucsc.edu/goldenPath/mm39/bigZips/)
Reference genome was indexed using [bwa-mem2](https://github.com/bwa-mem2/bwa-mem2) using: 
```
$ bwa-mem2 index reference.fa
```

### Read alignment
FASTQ files were aligned to mm39 reference genome using [bwa-mem2](https://github.com/bwa-mem2/bwa-mem2) with the following command:
```
$ bwa-mem2 mem -t <num_threads> reference.fa read_1.fastq read_2.fastq > alignment.sam
```
This results in a new .sam file format.

### Conversion, sorting, and deduplication
[Samtools](http://www.htslib.org/doc/samtools.html) was used for conversion to binary .bam format:
```
$ samtools view -bS alignment.sam > alignment.bam
```
Bam files were sorted with the following command:
```
$ samtools sort alignment.bam -o alignment.sorted.bam
```
Duplicate reads were marked using [GATK Picard Tools](https://gatk.broadinstitute.org/hc/en-us) with the following command:
```
$ gatk MarkDuplicates -M=metrics_file.txt -I=alignment.sorted.bam -O=alignment.sorted.duplicate.bam --REMOVE_DUPLICATES=false --ASSUME_SORTED=true --CREATE_INDEX=true
```

### Running DeepVariant
The [Nexflow implementation of DeepVariant](https://github.com/nf-core/deepvariant) was used for *de novo* variant calling. This requires installation of [Nextflow](https://www.nextflow.io/docs/latest/getstarted.html) and [docker](https://docs.docker.com/engine/install/ubuntu/). 
The program is executed with the following command:
```
$ nextflow run nf-core/deepvariant —fasta reference.fa --bam yourBamFile --bed yourBedFile -profile standard,docker
```
DeepVariant requires a bedfile specifying the regions that are considered for variant calling. For this, a bedfile for the whole genome assembly of m39 was downloaded from [UCSC Genome Browser](https://genome.ucsc.edu/cgi-bin/hgTables?hgsid=958424741_QcjJfjjsZqnW8Z6yxEFtPX2i37kd&clade=mammal&org=Mouse&db=mm39&hgta_group=map&hgta_track=refGene&hgta_table=0&hgta_regionType=genome&position=chr12%3A56%2C741%2C761-56%2C761%2C390&hgta_outputType=primaryTable&hgta_outFileName=).

