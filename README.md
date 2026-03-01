<<<<<<< HEAD
# VREfm-genomic-pipeline
Reproducible bioinformatics workflow for VREfm genomic analysis
=======
Title

Reproducible bioinformatics pipeline for genomic analysis of vancomycin-resistant Enterococcus faecium (VREfm)

1. Introduction

This repository provides the reproducible bioinformatics workflow used in our study:

Transmission dynamics and resistance gene dissemination of vancomycin-resistant Enterococcus faecium in a tertiary hospital in northeastern China.

The pipeline integrates short-read and long-read sequencing for genome assembly, antimicrobial resistance analysis, plasmid clustering, and phylogenetic inference.

2. Workflow Overview

The workflow includes:

Raw data quality control

Genome assembly

Assembly quality evaluation

Genome annotation

AMR gene detection

Plasmid prediction and clustering

Comparative genomics and phylogenetic analysis

3. Input

Illumina paired-end FASTQ and Nanopore FASTQ:

sample_R1.fastq.gz  
sample_R2.fastq.gz  
sample_nanopore.fastq.gz  
4. Software

All analyses were performed in Linux.

Tool	Version
FastQC	0.11.15
Trimmomatic	0.36
SPAdes	3.5.0
QUAST	5.2.0
CheckM	1.2.2
BUSCO	5.x
Canu	1.3
Unicycler	latest
GapFiller	
Prokka	1.14.6
Abricate	
MASH	
FastANI	
MOB-suite	
BLASTN	
BRIG	
Cytoscape	
ACT	
5. Step-by-step Commands
Step 1. Quality control
fastqc sample_R1.fastq.gz sample_R2.fastq.gz

Trimming:

trimmomatic PE \
sample_R1.fastq.gz sample_R2.fastq.gz \
sample_R1.clean.fastq.gz sample_R1.unpaired.fastq.gz \
sample_R2.clean.fastq.gz sample_R2.unpaired.fastq.gz \
SLIDINGWINDOW:4:20 MINLEN:50
Step 2. Genome assembly

Illumina:

spades.py \
-1 sample_R1.clean.fastq.gz \
-2 sample_R2.clean.fastq.gz \
-o spades_output

Nanopore:

canu \
-p sample \
-d canu_output \
genomeSize=3m \
-nanopore-raw sample_nanopore.fastq.gz

Hybrid assembly:

unicycler \
-1 sample_R1.clean.fastq.gz \
-2 sample_R2.clean.fastq.gz \
-l sample_nanopore.fastq.gz \
-o unicycler_output

Gap filling:

GapFiller.pl -l library.txt -s scaffolds.fasta -m 20 -o 10
Step 3. Assembly quality
quast.py contigs.fasta -o quast_output
checkm lineage_wf contigs/ checkm_output
busco -i contigs.fasta -l bacteria_odb10 -m genome
Step 4. Annotation
prokka contigs.fasta --outdir prokka_output
Step 5. AMR gene detection
abricate --db card contigs.fasta > amr.txt
Step 6. Plasmid prediction
mob_recon -i contigs.fasta -o mob_output
mob_typer -i plasmids.fasta -o mob_types.txt
Step 7. Plasmid clustering

Mash sketch:

mash sketch plasmids/*.fasta

Distance:

mash dist *.msh *.msh > mash_distance.txt

Clustering threshold:

Mash distance < 0.025

ANI validation:

fastANI \
-q plasmid1.fasta \
-r plasmid2.fasta \
-o ani.txt
Step 8. Phylogenetic analysis

Core genome alignment and phylogenetic tree construction were performed using standard comparative genomic workflows.

Step 9. Visualization

BRIG:

BRIG.pl

BLASTN:

blastn -query plasmid1 -subject plasmid2

ACT visualization.

6. Output

Genome assemblies

AMR profiles

Plasmid clusters

Phylogenetic trees

7. Reproducibility

All parameters and workflows are documented.
Assembly quality metrics are provided in the supplementary materials of the publication.
>>>>>>> b9f9c35 (VRE)
