Day 3 – Project Logbook
Date

18 December 2025

Total Time Spent

4 hours

Objectives

Develop a Python-based parser for filtering BLASTN results

Implement a reproducible and user-configurable filtration workflow using argparse

Strengthen understanding of environment management and result handling

Tasks Completed

BLAST Result Parsing and Filtration

Developed a Python script to parse the TSV file generated from BLASTN output.

Implemented filtering logic based on alignment parameters such as:

Percent identity

E-value

Alignment length and coverage

Ensured that the script processes raw BLAST results and outputs only high-confidence AMR gene hits.

Integration of argparse for Reproducibility

Used argparse to allow users to dynamically specify filtering parameters at runtime.

Enabled flexibility for users to define their own thresholds without modifying the source code.

Implemented default argument values to ensure smooth execution even when optional parameters are not explicitly provided.

Workflow Conceptualization

Established a clear two-step AMR detection workflow:

BLASTN generates raw alignment results in TSV format.

The TSV file is passed to a Python script where user-defined parameters (identity, E-value, etc.) are applied to filter confident AMR hits.

This approach improves efficiency, transparency, and reproducibility of the AMR-finder pipeline.

Result Handling and Output Management

Used pandas to read, visualize, and manipulate tabular BLAST output.

Added appropriate column headers to the TSV file during parsing.

Implemented logic to allow users to specify the exact output location for the filtered CSV/TSV file.

Ensured compatibility with execution via Anaconda Command Prompt.

Key Learnings

Gained a foundational understanding of environment creation using traditional Conda workflows.

Learned about UV-based environment creation, which simplifies dependency management by avoiding manual package downloads.

Reinforced prior knowledge of the pandas library, particularly:

Reading and visualizing CSV/TSV files

Adding headers to raw data

Filtering tabular data using conditional logic

Developed a strong conceptual understanding of argparse, including:

Creating user-friendly command-line arguments

Setting default values for optional parameters

Enabling reproducible and flexible execution of the AMR-finder tool

Understood how to direct filtered outputs to a user-defined file location programmatically.

Issues / Blockers Encountered

Initial difficulty in:

Properly initializing the Python script within the correct Conda environment

Understanding the workflow for environment creation using the UV-based approach

Conceptual challenge in fully grasping the internal working of argparse and how it integrates with the AMR-finder pipeline.

These issues were resolved through iterative testing and refinement of the script.

Outcome Summary

A functional, reusable Python filtering script was successfully developed.

The script enables configurable, command-line–driven filtration of BLASTN results.

This marks the completion of a core component of the AMR-finder pipeline.
