# Mini Project: Public Data Analysis of *Salmonella enterica* subsp. *enterica* serovar Typhimurium Using Whole-Genome Sequencing
 
## Project Overview

This mini project was developed as part of a beginner bioinformatics learning portfolio to explore how whole-genome sequencing (WGS) data can be used to analyze genomic variation in a zoonotic foodborne pathogen.

The project focuses on **publicly available WGS data of *Salmonella enterica* subsp. *enterica* serovar Typhimurium**, with the aim of identifying and annotating sequence variants using a **reference-based variant calling workflow**. Because *S. Typhimurium* is an important pathogen in poultry production, food safety, and public health, this project also serves as a small exercise in applying bioinformatics methods to a relevant **One Health** problem.

---

## Objective

To analyze **single nucleotide polymorphism (SNP) profiles** of publicly available *Salmonella enterica* subsp. *enterica* serovar Typhimurium isolates using a **reference-based whole-genome sequencing approach**, and to explore the distribution, annotation, and potential biological implications of the detected variants.

More specifically, this project aimed to:

* perform quality assessment and filtering of raw sequencing reads,
* align sequencing reads to a reference genome,
* identify genomic variants relative to the reference,
* annotate the variants to determine their genomic location and predicted functional effect,
* summarize mutation patterns and variant classes as an exploratory overview of genomic variation.

---

## Workflow Summary

The analysis followed a standard **reference-based bacterial WGS variant-calling pipeline**:

**Raw reads (FASTQ)**
→ **Quality control**
→ **Read trimming / filtering**
→ **Reference indexing**
→ **Read alignment to reference genome**
→ **SAM/BAM conversion and sorting**
→ **Variant calling**
→ **Variant filtering and normalization**
→ **Variant annotation with SnpEff**
→ **Variant summary and interpretation**

### Simplified workflow

**QC → Alignment → Variant Calling → Annotation**

---

## Tools Used

The following tools were used in this project:

* **FastQC** — initial quality assessment of raw reads
* **fastp** — read filtering / trimming
* **BWA-MEM** — alignment of reads to the reference genome
* **SAMtools** — SAM/BAM conversion, sorting, indexing, and mapping statistics
* **FreeBayes** — variant calling
* **bcftools** — filtering, normalization, and inspection of VCF files
* **SnpEff** — functional annotation of detected variants

---

## Input Data

* **Data source:** Public sequencing data from **NCBI SRA**
* **Target organism:** *Salmonella enterica* subsp. *enterica* serovar Typhimurium
* **Analysis approach:** Reference-based WGS variant analysis

### Reference genome

A publicly available *Salmonella Typhimurium* reference genome was used for mapping and annotation.

> **Note:** The reference genome used in this project is not derived from a strain identical to the analyzed sample isolate. Therefore, variant annotation and biological interpretation should be considered **exploratory** rather than definitive strain-specific mutation confirmation.

---

## Main Outputs

This workflow generated several main output files:

### Quality control outputs

* `fastqc.html` / `fastqc.zip`
* `fastp.html`
* `fastp.json`

### Alignment outputs

* `alignment.sam`
* `alignment.bam`
* `alignment_sorted.bam`
* `alignment_sorted.bam.bai`

### Variant calling outputs

* `variants_raw.vcf`
* `variants_filtered.vcf`
* `variants_norm.vcf`
* `variants_norm_renamed.vcf`

### Annotation outputs

* `variants_annotated.vcf`
* `snpEff_summary.html`
* `snpEff_summary.genes.txt`

---

## Key Findings

This mini project identified genomic variation across the analyzed *Salmonella Typhimurium* isolate(s) and provided an initial overview of their mutational profile.

### Summary of findings

* A total of **895 variants** were detected across the analyzed genome.
* Variant types were dominated by **SNPs**, with smaller numbers of MNPs, insertions, deletions, and mixed variants.
* Most annotated effects belonged to the **MODIFIER** impact category, indicating that many variants were located in **upstream, downstream, or intergenic regions**.
* Among coding-region variants, **missense variants** were more frequent than synonymous variants, suggesting that a subset of mutations may alter protein sequence.
* The mutation pattern showed a **transition bias**, with substitutions such as **C→T, G→A, A→G, and T→C** appearing among the most frequent base changes.
* These results suggest that the genome contains both **regulatory-region variation** and **protein-altering mutations** that may be relevant for bacterial adaptation and phenotype.

### Interpretation

Although many variants are likely neutral, some coding-region mutations may affect:

* **protein structure or function**,
* **bacterial adaptation**,
* **virulence-associated traits**, or
* potentially **antimicrobial resistance-related pathways**.

However, this project was designed as an **exploratory bioinformatics mini project**, so functional significance was **not experimentally validated**.

---

## Limitations

A key limitation of this analysis is the **reference genome choice**.

The reference genome used, ****Salmonella enterica* subsp. *enterica* serovar Typhimurium str. LT2 (GCF_000006945.2)***, is not derived from a strain identical to the analyzed sample isolate. This discrepancy may affect:

* the accuracy of **gene position annotation**,
* **codon change prediction**, and
* interpretation of the **predicted biological effects** of identified variants.

Therefore, the results presented here should be interpreted as an **exploratory overview of genomic variation**, rather than definitive evidence of specific causal mutations.

---

## Repository Structure

Example repository organization:

```bash
.
├── data/
│   ├── raw_reads/
│   ├── reference/
│   └── metadata/
├── results/
│   ├── qc/
│   ├── alignment/
│   ├── variants/
│   └── annotation/
├── scripts/
│   ├── 01_qc.sh
│   ├── 02_alignment.sh
│   ├── 03_variant_calling.sh
│   ├── 04_annotation.sh
│   └── 05_variant_report.sh
└── README.md
```

---

## How to Reproduce

Below is an example outline of how this analysis can be reproduced.

### 1. Prepare the working environment

Create and activate a conda environment containing the required tools:

```bash
conda create -n wgs fastqc fastp bwa samtools bcftools freebayes snpeff -c bioconda -c conda-forge
conda activate wgs
```

### 2. Perform quality control

Run FastQC on raw reads:

```bash
fastqc sample_R1.fastq.gz sample_R2.fastq.gz
```

Filter / trim reads with fastp:

```bash
fastp \
  -i sample_R1.fastq.gz \
  -I sample_R2.fastq.gz \
  -o sample_trimmed_R1.fastq.gz \
  -O sample_trimmed_R2.fastq.gz \
  -h fastp.html \
  -j fastp.json
```

### 3. Index the reference genome

```bash
bwa index reference.fna
samtools faidx reference.fna
```

### 4. Align reads to the reference

```bash
bwa mem reference.fna sample_trimmed_R1.fastq.gz sample_trimmed_R2.fastq.gz > alignment.sam
```

### 5. Convert, sort, and index BAM

```bash
samtools view -Sb alignment.sam > alignment.bam
samtools sort alignment.bam -o alignment_sorted.bam
samtools index alignment_sorted.bam
```

### 6. Call variants

```bash
freebayes -f reference.fna alignment_sorted.bam > variants_raw.vcf
```

### 7. Filter and normalize variants

Example filtering:

```bash
bcftools filter -i 'QUAL>=30 && DP>=10' variants_raw.vcf -o variants_filtered.vcf
```

Normalize:

```bash
bcftools norm -f reference.fna variants_filtered.vcf -o variants_norm.vcf
```
### 8. Annotate variants with SnpEff

```bash
snpEff ann \
  -stats snpEff_summary.html \
  <snpeff_database_name> \
  variants_norm_renamed.vcf > variants_annotated.vcf
```
### 9. Summarize annotated variants

Extract relevant annotation fields (gene, effect, impact, codon change, amino acid change) into a tabular report for easier interpretation.

---

## Disclaimer

This repository is intended as a **learning portfolio / mini project** for practicing WGS-based bacterial variant analysis using public sequencing data. The biological interpretation of the findings should not be considered clinical or diagnostic evidence without additional validation.

---

## Author

**Fahrunnisak Al-Firda Razak An-Haru**
Veterinarian | Molecular biology enthuasiat
