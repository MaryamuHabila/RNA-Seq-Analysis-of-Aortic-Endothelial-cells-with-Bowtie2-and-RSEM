# **A Beginner’s Guide to RNA-Seq Analysis with Bowtie2 and RSEM**

## **Introduction**
RNA sequencing (RNA-seq) is a powerful technique used to analyze gene expression levels across different conditions. This guide provides a step-by-step explanation of an RNA-seq pipeline using **Bowtie2** for read alignment and **RSEM** for transcript quantification. Designed for beginners, this document explains each command, its purpose, and the expected results, helping students and early-career bioinformaticians understand the computational aspects of transcriptomic analysis.

## **Pipeline Overview**
The pipeline processes paired-end RNA-seq data from raw FASTQ files to gene expression quantification. The main steps include:
1. **Preparing the Data**: Organizing raw sequencing reads.
2. **Read Alignment with Bowtie2**: Mapping reads to the reference genome.
3. **Transcript Quantification with RSEM**: Estimating gene and transcript expression levels.
4. **Output Files Interpretation**: Understanding the generated results.

---

## **Step 1: Organizing the Data**
Before running the analysis, we structure our files properly. The **samplesheet** lists our RNA-seq samples and their corresponding FASTQ files:

```
sample,fastq_1,fastq_2,strandedness
C1_311,/home/user/HAoECs_raw_fastq/C1_311_1.fq.gz,/home/user/HAoECs_raw_fastq/C1_311_2.fq.gz,reverse
C2_212,/home/user/HAoECs_raw_fastq/C2_212_1.fq.gz,/home/user/HAoECs_raw_fastq/C2_212_2.fq.gz,reverse
...
```

This ensures we track all samples and their corresponding read files.

---

## **Step 2: Running Bowtie2 and RSEM**
The following **Bash script** processes all samples using Bowtie2 for alignment and RSEM for quantification.

```bash
mkdir -p RSEM_Results

for sample in C1_311 C2_212 C2_312 C3_212 LD2_212 LD2_312 LD3_212 LD3_312
do
    echo "Processing sample: $sample"
    
    rsem-calculate-expression --paired-end \
        --bowtie2 \
        --strandedness reverse \
        --estimate-rspd --calc-ci \
        --output-genome-bam \
        --append-names --keep-intermediate-files \
        /home/user/HAoECs_raw_fastq/${sample}_1.fq.gz \
        /home/user/HAoECs_raw_fastq/${sample}_2.fq.gz \
        RSEM_Reference/Homo_sapiens_GRCh38 \
        RSEM_Results/${sample}
    
    echo "Finished processing: $sample"
done
```

### **Explanation of the Script**
- **`mkdir -p RSEM_Results`**: Creates a directory to store RSEM output files.
- **`for sample in ...`**: Loops through each sample name to process them individually.
- **`echo "Processing sample: $sample"`**: Prints progress updates.
- **`rsem-calculate-expression`**: The main command that:
  - `--paired-end`: Indicates paired-end sequencing reads.
  - `--bowtie2`: Uses Bowtie2 as the alignment tool.
  - `--strandedness reverse`: Specifies the strandedness of the library.
  - `--estimate-rspd --calc-ci`: Estimates read start position distribution and calculates confidence intervals.
  - `--output-genome-bam`: Saves the genome-aligned BAM file.
  - `--append-names --keep-intermediate-files`: Keeps intermediate files for further analysis.
- **`RSEM_Reference/Homo_sapiens_GRCh38`**: Reference genome and transcriptome for alignment.
- **`RSEM_Results/${sample}`**: Output directory for each sample's results.

---

## **Step 3: Understanding the Output Files**
After running the script, each sample will produce the following key output files in `RSEM_Results/`:

1. **`${sample}.genes.results`** - Gene-level expression estimates.
2. **`${sample}.isoforms.results`** - Transcript-level expression estimates.
3. **`${sample}.stat`** - Summary statistics about alignment.
4. **`${sample}.genome.bam`** - BAM file of aligned reads.

### **Interpreting the Results**
- **Gene Expression (TPM/FPKM values)**: Used for differential expression analysis.
- **Alignment Statistics**: Helps assess the quality of read alignment.
- **BAM Files**: Can be used for visualization in genome browsers.

---

## **Conclusion**
This guide walks through an RNA-seq workflow from raw sequencing reads to gene expression quantification using Bowtie2 and RSEM. By understanding each step, beginners can confidently process RNA-seq data and interpret the results for downstream analysis. This pipeline serves as a foundation for more advanced bioinformatics applications in transcriptomics.

