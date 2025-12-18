Day 1 – Project Logbook
Date

16 December 2025

Total Time Spent

3 hours

Objectives

Initialize the project repository structure

Configure the computational environment for AMR database construction

Generate a local BLAST-compatible AMR reference database

Tasks Completed

Repository Setup

Designed and initialized the repository skeleton.

Created major directories and placeholder files for future development.

Outlined the framework for AMR database generation and downstream analysis.

Environment Configuration

Set up environment variables using Anaconda Command Prompt.

Activated the project-specific Conda environment (amr-finder).

Verified successful installation of NCBI BLAST+ tools.

FASTA Processing and Database Creation

Concatenated multiple AMR gene FASTA files into a single file (amr_all.fasta).

Used makeblastdb to create a nucleotide BLAST database from the consolidated FASTA.

Successfully generated BLAST index files:

.nhr

.nin

.nsq

Confirmed that six AMR gene sequences were indexed without errors.

Blockers / Challenges Encountered

Gaining conceptual clarity on the internal working of makeblastdb, including:

Purpose of BLAST binary index files (.nhr, .nin, .nsq)

Relationship between FASTA input and BLAST database output

Understanding how to:

Concatenate multiple FASTA files using command-line tools

Build a BLAST database from a single consolidated FASTA file

Distinguishing between nucleotide and protein BLAST database workflows

Evidence / Validation

BLAST Installation Verification

(amr-finder) C:\Users\Lenovo\Downloads\amr-finder\amr_db\amr_genes.fasta> blastn -version
blastn: 2.17.0+
Package: blast 2.17.0, build Jul  1 2025 08:57:20


BLAST Database Creation

(amr-finder) C:\Users\Lenovo\Downloads\amr-finder\amr_db\amr_genes.fasta> makeblastdb -in amr_all.fasta -dbtype nucl -out amr_all_genes.fasta

Building a new DB, current time: 12/17/2025 00:19:39
New DB name:   C:\Users\Lenovo\Downloads\amr-finder\amr_db\amr_genes.fasta\amr_all_genes.fasta
New DB title:  amr_all.fasta
Sequence type: Nucleotide
Keep MBits: T
Maximum file size: 3000000000B
Adding sequences from FASTA; added 6 sequences in 0.0042493 seconds.

Outcome Summary

A functional nucleotide BLAST AMR reference database was successfully created.

The database is ready for downstream BLAST searches and result extraction.

The project environment and repository are now correctly initialized.
