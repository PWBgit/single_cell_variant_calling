# single-cell variant calling
Variant calling for single cell DNA sequencing

## Overview
In this project I am testing a strategy for variant calling in single cell DNA sequencing data. I am using data generated in ["Factors influencing meiotic recombination revealed by whole-genome sequencing of single sperm" - Hinch et al., Science 2019](https://science.sciencemag.org/content/363/6433/eaau8861/). The dataset includes short-read DNA sequencing data from bulk (many cells) and single sperm cells. In a fist step, Google Deep Variant, a deep learning algorithm, is used for *de novo* variant calling in a bulk sample (["A universal SNP and small-indel variant caller using deep neural networks" - Poplin et al., Nature Biotechnology 2018](https://www.nature.com/articles/nbt.4235)). In a second step ProSolo, a probabilistic model which jointly models single and bulk sequencing samples is used to call variants in signle cells (["ProSolo: Accurate Variant Calling from Single Cell DNA Sequencing Data" - Lähnemann et al., bioRxiv 2020](https://www.biorxiv.org/content/10.1101/2020.04.27.064071v1)). 

### Data Download
Data was downloaded from the [European Nucleotide Archive (ENA)](https://www.ebi.ac.uk/ena/browser/home) 
