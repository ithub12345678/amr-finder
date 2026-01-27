Fragment Reconstruction Module (Phase 3)
Overview

This module performs gene-level reconstruction of antimicrobial resistance (AMR) genes from fragment-level BLAST hits.
It addresses the common biological scenario where a single AMR gene is split across multiple fragments or contigs due to draft genome assemblies or sequencing limitations.

Unlike simple hit-based reporting, this module aggregates credible fragment evidence to infer gene-level presence and confidence in a conservative and explainable manner.

Purpose

After fragment-level confidence scoring (Phase 2), this module:

Identifies fragments belonging to the same reference AMR gene

Reconstructs gene-level coverage using reference-coordinate union

Aggregates statistical, structural, and biological evidence

Produces a final gene-level confidence score

This prevents:

false negatives caused by fragmented assemblies

false positives caused by naive fragment merging

Input

The module expects the output CSV from the scoring pipeline (scored_hits.csv) with at least the following columns:

Required columns:

qseqid – query contig ID

sseqid – reference AMR gene ID

sstart, send – alignment coordinates on the reference gene

slen – reference gene length

length – alignment length

Statistical_score

Deterministic_score

Biological_modifier_score

Only fragments with Deterministic_score > 0 are considered for reconstruction.

Reconstruction Logic
1. Fragment Grouping

Fragments are grouped by:

identical sseqid (reference gene)

2. Union Coverage Calculation

Reference gene coverage is computed as the union of aligned intervals (sstart–send), not a simple sum of fragment lengths.

Overlapping regions are not double-counted.

3. Reconstruction Gate

Fragments are reconstructed only if:

Combined union coverage ≥ 80%

Gaps between adjacent reference intervals do not exceed 50 bp

These thresholds are configurable.

4. Score Aggregation

Reconstructed Statistical Score
Coverage-weighted average of fragment statistical scores

Reconstructed Deterministic Score
Minimum deterministic score across fragments (conservative gate)

Reconstructed Biological Modifier
Soft adjustment based on combined coverage

Final confidence is computed as:

Final_gene_confidence =
    Recon_stat × Recon_det × Recon_bio

Output

The module produces a new CSV file (reconstructed_genes.csv) with one row per reconstructed gene.

Output columns include:

sseqid – AMR gene ID

num_fragments – number of fragments reconstructed

combined_coverage – union coverage fraction

Recon_stat – reconstructed statistical score

Recon_det – reconstructed deterministic score

Recon_bio – reconstructed biological modifier

Final_gene_conf – final gene-level confidence score

confidence_class – High / Medium / Low

Fragment-level CSV files are not modified.

Usage
python src/reconstruct.py \
  --input results/scored/scored_hits.csv \
  --output results/reconstructed/reconstructed_genes.csv

Design Principles

Conservative reconstruction: weak or noisy fragments cannot inflate confidence

Explainable scoring: every score component has biological meaning

No double counting: union coverage ensures accurate gene representation

Separation of concerns: fragment-level and gene-level results are reported separately

Limitations

This module performs evidence reconstruction, not sequence assembly

It does not predict ORFs, motifs, or functional expression

Reconstruction is limited to reference-based AMR genes

Pipeline Position
BLAST output
   ↓
filtered_hits.csv
   ↓
scored_hits.csv        (Phase 2)
   ↓
reconstructed_genes.csv (Phase 3)

Author Note

This reconstruction step significantly improves AMR detection robustness in fragmented genomes and represents the novel contribution of this project beyond standard BLAST-based pipelines.
