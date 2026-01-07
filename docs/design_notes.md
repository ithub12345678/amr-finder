# Design: Multi-Layer Confidence Scoring Framework for AMR Gene Detection

## Overview

After filtering biologically valid BLAST hits, we implement a **multi-layer confidence scoring framework** that integrates:

- Statistical evidence
- Deterministic structural integrity checks
- Biological context modifiers

The framework is designed to **balance sensitivity and specificity** while preserving interpretability and biological realism. It is particularly suited for fragmented assemblies, draft genomes, and metagenomic datasets where classical single-metric scoring approaches are insufficient.

---

## Motivation

AMR gene detection cannot be reliably inferred from a single metric such as percent identity or E-value alone. Strong statistical similarity does not guarantee that a detected gene is:

- Structurally intact  
- Biologically plausible  
- Consistent with known resistance gene behavior  

To address this, we adopt a **hybrid gated multiplicative framework** that requires collective agreement across three complementary evidence domains. This prevents overconfidence driven by any single factor while allowing partial but consistent evidence to contribute meaningfully.

---

## Confidence Framework

The framework consists of **three independent but complementary layers**:

1. **Statistical Factors (S)**
2. **Deterministic Structural Factors (D)**
3. **Biological Context Modifiers (B)**

Each layer answers a distinct biological question, and the final confidence score is computed through a **gated multiplicative combination**.

**Key design principle**  
Statistical evidence establishes existence, deterministic factors enforce structural integrity, and biological modifiers refine biological plausibility without dominating the score.

---

## Why Not a Simple Weighted Sum?

A simple weighted sum approach was intentionally avoided because:

- A single strong metric can dominate the final score
- Structural or biological implausibility may be masked by high identity
- Fragmented but biologically invalid hits may appear confident

Instead, a **multiplicative gating strategy** was adopted to ensure that:
- Structural and biological constraints actively limit confidence
- No single domain can independently inflate the final score

---

## Scoring Dimensions and Factors

## 1. Statistical Factors (S)

**Purpose**  
Eliminate random or weak alignments.

**Biological question answered**  
How unlikely is this alignment to have occurred by chance?

### Factors Used
- Normalized Percent Identity
- Normalized E-value

### a. Normalized Percent Identity

Percent identity from BLAST is normalized to a continuous range between 0 and 1:

identity_score = pident / 100

yaml
Copy code

This normalization improves:
- Scale compatibility across layers
- Interpretability
- Stability in downstream multiplication

---

### b. Normalized E-value

The E-value reflects the expected number of random hits.

Normalization formula:

evalue_score = min(1.0, -log10(evalue) / 50)

yaml
Copy code

- Extremely small E-values are capped
- Relative statistical strength is preserved
- Zero E-values are safely handled by replacement

---

### Statistical Score Formula

S = (0.6 × identity_score) + (0.4 × evalue_score)

yaml
Copy code

**Design rationale**
- Identity is weighted slightly higher to emphasize biological similarity
- E-value accounts for statistical exclusion of chance matches
- Weights sum to one for interpretability and normalization

---

## 2. Deterministic Structural Score (D)

**Purpose**  
Cap confidence for fragmented or structurally inconsistent gene alignments.

**Biological question answered**  
Is the gene structurally intact and coherently aligned?

These factors are **rule-based and non-probabilistic**, derived directly from BLAST alignment structure.

---

### a. Alignment Consistency (HSP-based)

Alignment consistency is inferred from the number of High-Scoring Pairs (HSPs):

| HSP Count | Interpretation                    | Score |
|---------|-----------------------------------|-------|
| 1       | Single continuous alignment       | 1.0   |
| 2       | Minor fragmentation               | 0.7   |
| ≥3      | Strong fragmentation              | 0.3   |

This penalizes discontinuous alignments indicative of gene breakage or partial hits.

---

### b. Deterministic Coverage

Defined as:

coverage = aligned_length / subject_length

yaml
Copy code

Discrete scoring thresholds:

| Coverage Range | Score |
|---------------|-------|
| ≥ 0.90        | 1.0   |
| 0.75 – 0.89   | 0.8   |
| 0.50 – 0.74   | 0.5   |
| < 0.50        | 0.0   |

---

### Deterministic Score Calculation

The deterministic score is computed as a **structural gate**:

D = min(alignment_consistency, deterministic_coverage)

yaml
Copy code

This ensures that:
- Both continuity and coverage constraints must be satisfied
- Structural weaknesses directly cap confidence

---

## 3. Biological Context Modifier (B)

**Purpose**  
Refine confidence using biological plausibility without asserting functionality.

**Biological question answered**  
Does this alignment behave like a real AMR gene biologically?

Biological modifiers **do not override** statistical or deterministic evidence. They only apply **soft penalties or adjustments**.

---

### a. Biological Coverage Plausibility

Defined as:

b_coverage = aligned_length / subject_length

yaml
Copy code

Discrete biological plausibility scores:

| Coverage Range | B_coverage |
|---------------|------------|
| ≥ 0.95        | 1.0        |
| 0.80 – 0.94   | 0.95       |
| < 0.80        | 0.85       |

This reflects known tolerance of AMR genes to small truncations while penalizing implausible lengths.

---

### b. Fragmentation Tolerance (Metadata-aware)

Fragmentation penalties are applied using curated metadata:

- Each gene is annotated as **fragment tolerant** or **not fragment tolerant**
- Metadata is derived from literature and curated databases

Penalty logic:

- If:
  - HSP count > 1  
  - AND gene is **not fragment tolerant**
- Then:
  - Apply a soft penalty (0.85)

Otherwise:
- No penalty applied

---

### Biological Modifier Formula

B = clip(B_coverage × B_frag, 0.8, 1.1)

yaml
Copy code

This ensures:
- Biological modifiers fine-tune confidence
- Scores remain bounded and non-dominant

---

## Important Biological Limitations

This framework **does not determine**:

- Protein functionality
- Gene expression
- Resistance phenotype
- Motif integrity
- ORF validity

It only evaluates **alignment-based biological plausibility** using BLAST results and curated metadata.

---

## Final Confidence Calculation

The final confidence score is computed as:

Final_confidence_score = S × D × B

yaml
Copy code

Scores are rounded for stability and interpretability.

---

## Interpretation of Final Confidence Score

| Score Range | Interpretation                          |
|------------|------------------------------------------|
| ≥ 0.80     | High confidence AMR gene detection        |
| 0.50–0.79  | Moderate confidence / partial evidence   |
| < 0.50     | Low confidence / likely false positive   |

---

## Summary of Layer Importance

- **Statistical Layer**: Establishes existence and excludes random matches  
- **Deterministic Layer**: Enforces structural integrity  
- **Biological Layer**: Refines plausibility using curated biological knowledge  

Together, these layers provide a **robust, interpretable, and biologically grounded confidence scoring framework** for AMR gene detection.

## Fragment Reconstruction Strategy
