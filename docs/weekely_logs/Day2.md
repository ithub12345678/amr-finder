Day 2 – Project Logbook
Date

17 December 2025

Total Time Spent

4 hours

Objectives

Develop a clear conceptual understanding of BLAST and makeblastdb

Resolve previous file-handling and scripting issues

Execute a controlled BLASTN test and generate structured output

Tasks Completed

Conceptual Understanding

Gained a clear understanding of the internal working of makeblastdb and its role in creating BLAST-compatible databases.

Reviewed previously used modules and corrected misunderstandings from Day 1.

Error Resolution

Identified and fixed a file extension error related to the amr_all_gene.fasta.n* BLAST database files.

Ensured correct naming and referencing of BLAST database prefixes.

BLASTN Exploration

Explored the functionality of blastn using the -help option.

Studied key BLASTN parameters, including:

Query input file (-query)

Database selection (-db)

Output formatting (-outfmt)

Compared different output formats such as:

Pairwise alignment

Tabular (TSV) format

Custom column selection using outfmt 6

TSV Output Generation

Created a dedicated results directory inside the amr_db folder.

Generated a tab-separated values (TSV) file using a customized outfmt 6 specification.

Selected specific columns relevant for AMR interpretation (identity, alignment length, coverage-related fields, E-value, bit score).

BLASTN Test Execution

Designed a test query sequence by combining:

The E. coli reference genome

An artificially introduced AMR gene (BLMT)

Ran BLASTN against the custom AMR database created using makeblastdb.

Successfully redirected BLAST output to a file instead of the terminal.

Blockers / Challenges Encountered

The primary issue was related to script formatting in Windows Anaconda Prompt:

Initially used the Linux-style line continuation character (\), which is not supported in Windows CMD.

As a result, only the first line of the BLAST command executed, and subsequent options (including output file specification) were ignored.

Due to this, BLAST results were printed to the terminal instead of being written to results.tsv.

After identifying the issue, corrected the command syntax by:

Using the appropriate Windows-compatible format

Ensuring proper command execution and file creation in the intended directory

Evidence / Validation

Excerpt from the first few lines of results.tsv:

NC_000913.3	AY220558.1	95.918	98	4641652	4042	3365549	3365645	3084	3180	2.88e-37	158
NC_000913.3	AY220558.1	95.876	97	4641652	4042	3652029	3652125	3085	3180	1.04e-36	156
NC_000913.3	AY220558.1	95.833	96	4641652	4042	3131251	3131346	3180	3086	3.73e-36	154
NC_000913.3	AY220558.1	95.833	96	4641652	4042	1300604	1300699	3180	3086	3.73e-36	154
NC_000913.3	AY220558.1	96.774	93	4641652	4042	574588	574680	3089	3180	3.73e-36	154


(Screenshots documenting command execution and output verification were recorded separately.)

Outcome Summary

Successfully generated a structured BLASTN result file (results.tsv) in the intended directory.

Confirmed correct use of BLAST command syntax in a Windows environment.

Established a reproducible workflow for BLAST execution and result extraction.

Developed clarity on interpreting partial and conserved AMR gene hits./>
