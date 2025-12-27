Design: Multi-Layer Confidence Scoring Framework for AMR Gene Detection
Overview

After filtering biologically valid hits, we introduce a multi-layer confidence scoring framework that integrates:

Statistical evidence

Deterministic gene integrity checks

Biological context modifiers

This design prevents overconfidence from any single evidence domain while preserving interpretability, robustness, and biological realism, especially for fragmented assemblies, draft genomes, and metagenomic data.

Motivation

AMR gene detection cannot be reliably determined using a single metric such as percent identity or E-value alone. Strong evidence in one domain does not guarantee that a detected gene is complete, structurally coherent, or biologically plausible.

To address this, we adopt a hybrid gated multiplicative framework that requires collective satisfaction across three domains, rather than allowing strong evidence from one domain to dominate the final confidence score.

Confidence Framework

The framework consists of three layers:

Statistical Factors

Deterministic Structural Factors

Biological Context Modifiers

Each layer answers a distinct biological question, and the final confidence score is obtained through a gated multiplicative combination.

Key design principle:
Statistical evidence establishes existence, deterministic factors enforce gene integrity, and biological modifiers refine plausibility.

Why Not a Simple Weighted Sum?

A standard weighted sum approach was intentionally avoided because:

A single low score can drive the entire result to zero

This leads to over-penalization, especially in:

Fragmented assemblies

Draft genomes

Metagenomes

Instead, a hybrid gated multiplicative method was implemented to enforce integrity while allowing partial evidence to contribute meaningfully.

Scoring Dimensions and Factors
1. Statistical Factors (S)

Purpose: Eliminate clear random matches
Question answered: How unlikely is this alignment to be random?

Factors used:

Normalized Percent Identity

Normalized E-value

a. Normalized Identity

Percent identity from BLAST is normalized to a continuous value between 0 and 1:

identity_score = pident / 100


This normalization improves:

Consistency

Interpretability

Compatibility with other scoring layers

b. Normalized E-value

The E-value represents the expected number of hits occurring by chance.

Normalization formula:

evalue_score = min(1.0, -log10(evalue) / 50)


This caps extremely small E-values while preserving relative strength.

Statistical Score Formula
S = w1 × identity_score + w2 × evalue_score


Where:

w1 + w2 = 1

Default weighting may be adjusted depending on dataset characteristics. The statistical score was computed as a weighted sum of normalized percent identity and normalized E-value. Identity was given slightly higher weight (0.6) to emphasize biological similarity, while E-value (0.4) accounted for statistical chance exclusion. The weights sum to one to maintain score normalization and interpretability.

Example

Given:

pident = 92.4

evalue = 1e-40

identity_score = 0.924
evalue_score   = 0.8


Statistical score is computed using the weighted sum formula.

Interpretation:

Identity reflects biological similarity

E-value reflects chance exclusion

2. Deterministic Structural Score (D)

Purpose: Cap confidence for truncated or fragmented genes
Question answered: Is the gene structurally intact?

These factors are non-probabilistic, rule-based checks derived directly from BLAST alignments.

a. Length Consistency

Defined as:

length_consistency = aligned_query_length / reference_gene_length


Discrete score values are assigned based on predefined ratio ranges.

b. Subject Coverage

Defined as:

subject_coverage = aligned_subject_length / subject_length


Interpretation:

High subject coverage (~100%):
Suggests near full-length gene matches, likely orthologs or variants.

Low subject coverage:
May indicate fragments, conserved domains, or large INDELs.

c. Alignment Consistency

Based on the number and structure of HSPs:

Single continuous HSP → highest confidence

Two HSPs with gaps → reduced confidence

≥3 HSPs → low confidence

Fragmentation penalties reflect reduced structural coherence.

Deterministic Score Calculation

Each deterministic factor is scored discretely and combined as:

D = mean(length_consistency_score,
         subject_coverage_score,
         alignment_consistency_score)

3. Biological Context Modifier (B)

Purpose: Adjust confidence based on biological plausibility
Question answered: Does this gene behave like a real AMR gene biologically?

Biological modifiers do not dominate the score and do not assert functionality.

Gene Length Consistency (Biological)

Defined as:

length_ratio = aligned_subject_length / expected_gene_length


Discrete scoring rules are applied based on proximity to known gene lengths.

This requires:

No motif detection

No ORF prediction

Only BLAST results + metadata

This alone is a strong biological plausibility check.

Important Limitation

These modifiers do not determine:

Whether the protein is functional

Whether resistance is expressed

Whether motifs confer resistance

Whether transcription occurs

They only adjust confidence based on biological realism.

Final Score Calculation

The final confidence score is computed using a gated multiplicative formula:

Final Score = (S × D) × B
Final Score = min(Final Score, 1.0)

Interpretation of the Final Score
Score Range	Interpretation
≥ 0.80	High confidence
0.50 – 0.79	Medium confidence
< 0.50	Low confidence / likely false positive
Summary of Layer Importance

Statistical Gate: Eliminates random alignments

Deterministic Factors: Enforce gene structural integrity

Biological Modifiers: Refine plausibility without overreach

Together, this framework produces a robust, interpretable, and biologically defensible confidence score suitable for AMR detection in real-world genomic data.
