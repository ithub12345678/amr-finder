Day Log – Theory and Design Work
Date - 22/12/25
Time Spent - 4 hours
Objective

To design a robust and interpretable confidence scoring framework for AMR gene detection that integrates statistical evidence, deterministic gene integrity checks, and biological plausibility, while minimizing false positives and avoiding over-penalization in fragmented assemblies and metagenomic data.

Work Completed
1. Problem Analysis

Identified limitations of single-metric AMR detection, particularly reliance on percent identity or E-value alone.

Recognized frequent false positives arising from:

Conserved domains

Partial gene matches

Fragmented assemblies and draft genomes

Concluded that AMR gene presence cannot be asserted from a single evidence domain.

2. Conceptualization of Multi-Layer Confidence Framework

Designed a three-domain confidence model consisting of:

Statistical factors

Deterministic structural factors

Biological context modifiers

Each domain was mapped to a specific biological question:

Statistical: How unlikely is the hit to be random?

Deterministic: Is the gene structurally intact?

Biological: Does the hit behave like a real AMR gene biologically?

3. Justification for Hybrid Gated Multiplicative Design

Rejected simple weighted-sum models due to:

Over-penalization (score collapsing to zero)

Poor performance on fragmented and metagenomic data

Implemented a hybrid gated multiplicative framework to:

Enforce gene integrity

Prevent domination by a single evidence domain

Preserve interpretability

4. Definition of Statistical Scoring (S)

Selected normalized percent identity and normalized E-value as primary statistical evidence.

Defined normalization strategies:

identity_score = pident / 100

evalue_score = min(1.0, -log10(evalue) / 50)

Combined statistical evidence using a weighted additive model to avoid redundancy amplification.

5. Definition of Deterministic Structural Scoring (D)

Formalized deterministic checks derived directly from BLAST outputs:

Subject coverage

Length consistency

Alignment (HSP) consistency

Implemented discrete scoring bins instead of continuous penalties.

Combined deterministic scores using a mean-based aggregation to allow partial credit and reduce harsh penalties.

6. Definition of Biological Context Modifiers (B)

Identified gene length consistency as the most defensible biological modifier.

Designed a plausibility-based modifier that:

Requires no motif detection

Requires no ORF prediction

Uses only BLAST + reference metadata

Explicitly documented limitations:

Does not assert gene functionality

Does not infer resistance phenotype

7. Final Confidence Formula

Defined final confidence score as:

Final Score = (Statistical Score × Deterministic Score) × Biological Modifier


Applied score capping to ensure values remain within [0, 1].

Established interpretation thresholds for high, medium, and low confidence.

Key Design Decisions

Statistical evidence establishes existence, not completeness.

Deterministic checks enforce structural validity.

Biological modifiers refine plausibility without dominance.

Multiplicative gating prevents overconfidence from any single domain.

Key Learnings

Most false positives arise from structural incompleteness, not statistical weakness.

Biological plausibility must refine, not override, statistical evidence.

Interpretability is as important as accuracy for downstream users.
