
**PT_identify_polyA_sites** is a Python-based tool designed to identify and analyze poly(A) sites in RNA-seq data. 
This tool processes BAM files to extract poly(A) sites and performs statistical analysis to compare the poly(A) 
site distributions between wild-type (WT) and mutant (MUT) samples.

## Quick start - example usage

Nanopore direct RNAseq reads need to be mapped to the transcriptome with the UTR sequences. This would correspond to this in the example "transcript_with_UTR.fa". Then for the input for this tool --reference_transcript  need sot the the same sequence_ID names but with out the UTR.
These sequnces are used to index where the stop codon is. 

## Example

```bash
python extract_polyA_sites_mapped_to_transcriptome_with_UTR.py --bam transcriptome_tests/test.bam 
--fasta transcriptome_tests/transcript_with_UTR.fa --polyA_length 7 
--reference_transcript transcriptome_tests/transcript.fa
--fdr 0.05 --log analysis.log --log-level INFO --output results.tsv

```

## Features

- Extract poly(A) sites from BAM files mapped to transcriptome sequences.
- Compare the distances of poly(A) start site to the stop codon between WT and MUT groups.
- Perform statistical tests, including Mann-Whitney U test and False Discovery Rate (FDR) correction. And Earth-mover distance
- Generate summary statistics for both overall and significant transcripts.


# PolyA Site Extraction and Analysis

This project provides scripts for extracting poly(A) sites from nanopore direct RNAseq data, performing statistical analysis, and identifying differences between wild-type (WT) and mutant (MUT) groups.

## Requirements

- Python 3.6+
- `pysam` (for handling BAM and FASTA files)
- `pandas` (for data manipulation)
- `scipy` (for statistical tests)
- `statsmodels` (for multiple testing correction)
- `nose2` (for running unit tests)
- `biopython` for the transcriptome approach 

You can install the required Python packages using `pip`:

```bash
pip install pysam pandas scipy statsmodels nose2 biopython
```

## unit test
in the parent directory run

```bash
nose2
```

## file structure

```bash

.
├── identify_polyA
│   ├── extract_stop_codon_positions.py
│   ├── parse_gtf.py
├── scripts
│   ├── extract_polyA_sites.py
├── tests
│   ├── test_extract_stop_codon_positions.py
├── README.md

```
### transcritpome approach (genome version is NOT WORKING YET!)

```
python extract_polyA_sites_mapped_to_transcriptome_with_UTR.py \
    --bam <BAM_FILES> \
    --fasta <FASTA_FILE> \
    --reference_transcript <REFERENCE_TRANSCRIPT_FILE> \
    --polyA_length: Minimum length of poly(A) tail to consider \
    --groups <GROUP_NAMES> \
    --log: Log file to store the logging information. '
    --log-level: Logging level (DEBUG, INFO, WARNING, ERROR, CRITICAL). \
    --output <OUTPUT_PREFIX>

```


### Arguments more indepth info 
    --bam: List of BAM files to be parsed (e.g., --bam file1.bam file2.bam file3.bam).
    --fasta: FASTA file of the reference genome.
    --reference_transcript: FASTA file of the reference transcripts with UTR.
    --groups: List of group names corresponding to BAM files (e.g., --groups WT WT WT MUT MUT MUT).
    --output: Output prefix for the generated TSV files.
    --polyA_length: Minimum length of poly(A) tail to consider (default: 10).
    --fdr: False discovery rate threshold for multiple testing correction (default: 0.05).
    --log: Log file to store the logging information (default: script.log).

### Output
The script generates three main output files:

1) WT_<output_prefix>.tsv: Poly(A) site information for the WT group.
2) MUT_<output_prefix>.tsv: Poly(A) site information for the MUT group.
3) significant_<output_prefix>.tsv: Significant transcripts with different poly(A) site locations between WT and MUT groups after FDR correction.
Each TSV file contains the following columns:

- `Read_Name`: Name of the read.
- `TranscriptID`: Identifier of the transcript.
- `Genomic_Coordinate`: Genomic coordinate of the poly(A) site.
- `PolyA_Start`: Start position of the poly(A) tail.
- `PolyA_Length`: Length of the poly(A) tail.
- `Pre_PolyA_Sequence_From_Read`: Sequence preceding the poly(A) site from the RNAseq read.
- `Pre_PolyA_Sequence_From_Ref`: Sequence preceding the poly(A) site from the reference genome.
- `Distance_to_Stop`: Distance from the poly(A) site to the stop codon.

### Statistical Analysis
The tool performs statistical analysis to compare poly(A) site distributions between WT and MUT groups. The analysis includes the following steps:

1) Extract Poly(A) Sites:
 - Identify poly(A) sites from BAM files.
 - Filter reads to only consider poly(A) sites located after the stop codon.
2) Per-Transcript Statistical Comparison:
 - Use Mann-Whitney U test to compare the distances from poly(A) sites to the stop codon between WT and MUT groups.
 - Apply FDR correction to account for multiple testing.
3) Global Statistical Analysis:
 - Calculate the Wasserstein distance between WT and MUT poly(A) sites.
 - Perform additional global statistical tests, including Mann-Whitney U test.
 - Generate summary statistics, including count, mean, median, standard deviation, minimum, maximum, mode, and interquartile range (IQR).


Logging
The script logs its progress and important information to a log file specified by the --log argument (default: script.log). The log file contains details about the processing of BAM files, the number of poly(A) sites extracted, and the results of statistical tests.

## Example

```bash
python extract_polyA_sites_mapped_to_transcriptome_with_UTR.py --bam transcriptome_tests/test.bam --fasta transcriptome_tests/transcript_with_UTR.fa --polyA_length 7 --fdr 0.05 --log analysis.log --log-level INFO --output results.tsv --reference_transcript transcriptome_tests/transcript.fa
```

# Genome version (not yet working)

### Scripts Description
1. identify_polyA/parse_gtf.py
This script contains the function parse_gff_gft for parsing GTF/GFF files.

2. identify_polyA/extract_stop_codon_positions.py
This script contains the function extract_stop_codon_positions which extracts stop codon positions from a GTF file.

3. scripts/extract_polyA_sites.py
This is the main script that:

Extracts poly(A) sites from BAM files.
Calculates distances from stop codons.
Performs statistical analysis to identify differences between WT and MUT groups.
Usage
1. Extracting Stop Codon Positions
To extract stop codon positions, you need to use the extract_stop_codon_positions function from the identify_polyA module.

2. Extracting PolyA Sites and Performing Statistical Analysis
Run the main script extract_polyA_sites.py with the following command-line arguments:

--bam: List of BAM files to be parsed (e.g., --bam file1.bam file2.bam file3.bam)
--output: Output TSV file to store the poly(A) sites
--fasta: FASTA file of the reference genome
--gtf: GTF file with genomic annotations
--groups: List of group names corresponding to BAM files (e.g., --groups WT WT WT MUT MUT MUT)
--fdr: False discovery rate threshold for multiple testing correction (default: 0.05)
--log: Log file to store the logging information (default: script.log)

### Example Usage

```bash
python scripts/extract_polyA_sites.py --bam file1.bam file2.bam file3.bam --output polyA_sites.tsv --fasta reference.fasta --gtf annotations.gtf --groups WT WT WT MUT MUT MUT --fdr 0.05 --log script.log
```



# Generating the test files and example bam file. 


## Map the FASTA sequences to the reference and convert to BAM directly
minimap2 -a -x sr -k 7 -w 1 -A 1 -B 2 -O 2,16 -E 4,1 -s 40 --secondary=no \
transcript_with_UTR.fa reads.fa | samtools view -b -o tmp.bam

## Sort the BAM file
samtools sort -o test.bam tmp.bam

## Index the sorted BAM file
samtools index test.bam

testing command
```bash
python extract_polyA_sites_mapped_to_transcriptome_with_UTR.py --bam transcriptome_tests/test.bam --reference_transcript transcriptome_tests/transcript.fa --fasta transcriptome_tests/transcript_with_UTR.fa --group WT --output output.test

```