# **Introduction to Bioinformatics: Read Mapping and Genome Assembly**

## **Project Overview**
This project is a comprehensive exploration of essential bioinformatics workflows, focusing on read mapping, quality control, and genome assembly. It utilizes practical tools such as the SRA Toolkit, FastQC, SPAdes, and BWA to analyze and process *E. coli* whole-genome sequencing (WGS) data. Developed as part of my "Introduction to Bioinformatics" course, it provides a hands-on experience in key bioinformatics techniques for analyzing genomic data.

## **Table of Contents**
1. [Project Objectives](#project-objectives)
2. [Data Acquisition](#data-acquisition)
3. [Part A - Quality Control and Preliminary Analyses](#part-a---quality-control-and-preliminary-analyses)
4. [Part B - De Novo Genome Assembly](#part-b---de-novo-genome-assembly)
5. [Part C - Read Mapping](#part-c---read-mapping)
6. [Results and Interpretation](#results-and-interpretation)
7. [Tools and Dependencies](#tools-and-dependencies)
8. [Repository Structure](#repository-structure)
9. [Credits](#credits)

## **Project Objectives**
The main goals of this project were to learn and apply fundamental bioinformatics techniques to analyze short-read sequencing data, perform quality control, assemble a draft genome, and map sequencing reads to a reference genome. Throughout this project, I worked with *E. coli* sequencing data, leveraging a variety of bioinformatics tools to gain practical experience.

---

## **Data Acquisition**
The primary data used in this project is the short-read whole-genome sequencing (WGS) data of *E. coli*. The sequence data was downloaded from the SRA (Sequence Read Archive) database using the SRA Toolkit. The specific dataset used was **SRR8185316**, which contains raw reads of *E. coli* WGS data.

To download the data:
```bash
fastq-dump SRR8185316
```

The command retrieves the sequencing data in the FASTQ format, which is then used in subsequent steps for quality control and analyses.

---

## **Part A - Quality Control and Preliminary Analyses**
This part focuses on understanding the raw sequencing data and performing quality control to ensure the data is ready for analysis.

1. **Analyzing the FASTQ File:**
   - Count the total number of reads.
   - Extract the identifier, sequence, and quality score of the first read.
   - Count the occurrences of a specific sequence motif (e.g., `TTAAATGGAA`).
   - Extract and save the first 1000 sequences to a separate file for initial inspection.

2. **Quality Control:**
   - Perform a quality assessment using FastQC to visualize read quality.
   - Generate box plots of quality scores across base positions to identify potential issues.
   - Visualize read length distributions using density plots.

3. **Steps to Run FastQC:**
   ```bash
   fastqc SRR8185316.fastq
   ```
   This command generates an HTML report highlighting various aspects of the data quality, such as per-base sequence quality, GC content, sequence length distribution, and adapter content.

---

## **Part B - De Novo Genome Assembly**
De novo genome assembly reconstructs the genome without relying on a reference. Here, the SPAdes assembler is used to generate a draft assembly.

1. **Assembling the Genome:**
   - Use SPAdes to assemble the raw reads into contigs (continuous sequences).
   - SPAdes uses a multi-step approach that corrects errors, assembles contigs, and creates scaffolds.

2. **Steps to Run SPAdes:**
   ```bash
   spades.py -s SRR8185316.fastq -o spades_output
   ```
   This command produces the assembled genome contigs in the `spades_output` directory.

3. **Quality Assessment of Assembly:**
   - Use Quast to evaluate the quality of the genome assembly by comparing the contigs to a reference genome.
   - Quast provides metrics like N50, total contig length, and misassemblies to assess the accuracy and completeness of the assembly.

4. **Steps to Run Quast:**
   ```bash
   quast.py -r reference_genome.fasta -o quast_output spades_output/contigs.fasta
   ```
   The `quast_output` folder contains a detailed report on the assembly’s quality and how it compares to the reference genome.

---

## **Part C - Read Mapping**
Read mapping involves aligning sequencing reads to a reference genome to identify their locations.

1. **Mapping Reads with BWA:**
   - Use BWA (Burrows-Wheeler Aligner) to map the *E. coli* short reads to a reference genome.
   - BWA produces a SAM file containing the mapping information.

2. **Steps to Run BWA:**
   ```bash
   bwa index reference_genome.fasta
   bwa mem reference_genome.fasta SRR8185316.fastq > mapped_reads.sam
   ```

3. **Converting SAM to BAM Format:**
   - Use Samtools to convert the SAM file into a BAM file, sort it, and index it for efficient storage and retrieval.
   ```bash
   samtools view -bS mapped_reads.sam > mapped_reads.bam
   samtools sort mapped_reads.bam -o mapped_reads_sorted.bam
   samtools index mapped_reads_sorted.bam
   ```

4. **Visualizing Mapped Reads:**
   - Use IGV (Integrative Genomics Viewer) to visually inspect the mapped reads on the reference genome.
   - Choose a genomic region of interest and load the BAM file in IGV to explore coverage and alignment.

5. **Calculating Mapping Statistics:**
   - Use Samtools to calculate the percentage of reads mapped to the reference genome.
   ```bash
   samtools flagstat mapped_reads_sorted.bam
   ```
   - Calculate the read depth at all positions of the reference genome.
   ```bash
   samtools depth mapped_reads_sorted.bam > depth.txt
   ```

---

## **Results and Interpretation**
- **Quality Control Results**: FastQC results showed high-quality reads with consistent base quality scores. The GC content and sequence length were uniform, indicating good data quality.
- **Genome Assembly Quality**: The assembly generated using SPAdes was of high quality, with a large N50 and minimal misassemblies as reported by Quast.
- **Read Mapping Efficiency**: BWA successfully mapped the majority of the reads to the reference genome, with a high mapping percentage and consistent read depth, as calculated by Samtools and visualized in IGV.

---

## **Tools and Dependencies**
- **SRA Toolkit**: For downloading sequencing data from the SRA.
- **FastQC**: For assessing the quality of sequencing reads.
- **SPAdes**: For performing de novo genome assembly.
- **Quast**: For evaluating the quality of the genome assembly.
- **BWA**: For mapping short reads to a reference genome.
- **Samtools**: For processing SAM/BAM files.
- **IGV**: For visualizing mapped reads.

---

## **Repository Structure**
```
introduction-to-bioinformatics/
├── data/
│   └── SRR8185316.fastq
├── scripts/
│   ├── download_data.sh
│   ├── quality_control.py
│   ├── genome_assembly.sh
│   └── read_mapping.sh
├── notebooks/
│   └── bioinformatics_analysis.ipynb
├── results/
│   ├── fastqc_report.html
│   ├── spades_output/
│   ├── quast_report.pdf
│   └── mapped_reads.bam
├── README.md
└── LICENSE
```

---

## **Credits**
This project was developed as part of the "Introduction to Bioinformatics" course under the guidance of Dr. Hesam Montazeri, with support from TAs Sajedeh Bahonar and Mohammad Sadegh Vafaei.
