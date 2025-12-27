# Day Log

## Day 6 – Confidence Scoring Framework (Statistical + Deterministic)
**Date:** 27-12-25
**Time Spent:** ~4 hours  

---

## Objective
Design, clean, and implement a robust multi-layer confidence scoring system for filtered AMR BLAST hits, ensuring statistical rigor, deterministic validation, and reproducible computation.

---

## Tasks Completed

### 1. Statistical Scoring Layer
- Implemented a **statistical confidence score** using:
  - Percent identity (`pident`) normalized to [0–1]
  - E-value transformed using `-log10(evalue)` with safe handling for zero values
- Applied a **weighted linear combination**:
  - Identity weight = 0.6 (biological similarity emphasis)
  - E-value weight = 0.4 (statistical significance)
- Ensured vectorized computation for scalability and performance.

---

### 2. Deterministic Scoring Layer
Implemented deterministic checks to validate alignment integrity beyond raw statistics:

- **Alignment Consistency**
  - Counted HSPs per `(qseqid, sseqid)` pair
  - Assigned penalties for fragmented alignments:
    - 1 HSP → 1.0
    - 2 HSPs → 0.7
    - >2 HSPs → 0.3

- **Length Consistency**
  - Evaluated `qlen / slen` ratio
  - Assigned discrete confidence tiers to penalize partial or truncated matches

- **Subject Coverage**
  - Evaluated `alignment_length / slen`
  - Ensured sufficient coverage of the reference AMR gene

- Combined all three deterministic components using **equal weighting**.

---

### 3. Final Confidence Score
- Integrated statistical and deterministic layers using a **gated multiplicative model**:
  

- This approach ensures:
- High confidence only when **both** statistical evidence and deterministic validity are satisfied
- Prevention of overconfidence driven by a single strong metric

---

## Key Improvements Over Initial Draft

- Removed global state (`df_copy`) and side effects
- Corrected vector vs scalar logical errors
- Eliminated unreachable code paths
- Ensured all scoring functions return aligned pandas Series
- Improved code readability, modularity, and reproducibility
- Maintained biological interpretability of each scoring component

---

## Key Learnings

- Confidence scoring for AMR detection must be **multi-dimensional**
- High sequence identity alone is insufficient without alignment integrity
- Deterministic filters are essential to avoid false positives caused by fragmented hits
- Multiplicative gating is effective for enforcing cross-domain agreement
- Writing pure functions significantly improves debugging and future extensibility

---

## Next Steps

- Integrate **biological context modifiers**:
- ORF completeness (start/stop codons)
- Motif/domain conservation
- Gene family–specific thresholds
- Expose scoring parameters via **argparse CLI**
- Add unit tests for each scoring layer
- Benchmark scoring behavior across known true/false AMR hits

---
