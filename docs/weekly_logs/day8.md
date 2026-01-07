# Day 8 – Weekly Progress Log
DATE - 07-01-2026
TIME SPENT - ~3-4 hrs

## Overview
Today’s work focused on **finalizing and integrating a biologically informed confidence-scoring framework** for the AMR detection pipeline. The primary objective was to combine **statistical**, **deterministic**, and **biological modifier** scores into a unified, interpretable final confidence score while ensuring modularity, robustness, and biological realism.

---

## Work Completed

### 1. Metadata Preparation (Biological Context)
- Created a **comprehensive metadata file** for antimicrobial resistance (AMR) genes.
- Metadata fields include:
  - Resistance gene identifiers
  - Mechanism of resistance
  - Functional properties
  - Minimum functional coverage thresholds
  - Fragment tolerance (whether a gene remains functional when fragmented)
- Primary data sources:
  - Peer-reviewed publications
  - PubMed / NCBI gene sequence databases
- This metadata is intended to serve as a **core biological knowledge layer** for downstream scoring.

---

### 2. Scoring Framework Design
The scoring system was structured into **three independent but complementary components**, each implemented as a separate function for clarity and maintainability.

#### A. Statistical Score
- Based on:
  - Percent identity
  - E-value (log-scaled and capped)
- Weighted combination:
  - Identity: 0.6
  - E-value: 0.4
- Purpose:
  - Capture alignment confidence using conventional BLAST-derived statistics.

#### B. Deterministic Score
- Based on:
  - Alignment consistency (HSP count)
  - Subject coverage
- Key ideas:
  - Penalize fragmented or inconsistent alignments.
  - Use discrete thresholds to maintain interpretability.
- Final deterministic score is derived by gating coverage with alignment consistency.

#### C. Biological Modifier Score
- Designed as a **soft biological plausibility modifier**, not a dominant scoring factor.
- Components:
  - Gene length plausibility (coverage-based)
  - Fragmentation tolerance (metadata-aware)
  - High-Scoring Pair (HSP) count
- Important design principle:
  - The biological modifier **fine-tunes** the final score but never overrides strong statistical or deterministic signals.
- Metadata integration:
  - Fragment tolerance information is merged using accession IDs.
  - Fragmented alignments for non-fragment-tolerant genes are penalized.

---

### 3. Final Confidence Calculation
- Combined scoring model:
Final_confidence_score =
Statistical_score
× Deterministic_score
× Biological_modifier_score

- Scores are clipped and rounded to maintain numerical stability and interpretability.

---

### 4. Code Quality and Usability Improvements
- Refactored code for:
- Improved readability
- Modular design
- Reduced redundancy
- Each scoring function:
- Operates on a copy of the filtered DataFrame
- Avoids side effects
- Introduced `argparse` for:
- Input BLAST CSV file
- Metadata file
- Output file path
- Removed all hard-coded paths to ensure:
- Reproducibility
- Ease of use
- Pipeline portability

---

## Problems Encountered

1. **Conceptual overlap of coverage**
 - Coverage is used in both deterministic and biological scoring.
 - Required careful distinction between:
   - Deterministic alignment completeness
   - Biological plausibility of gene functionality

2. **HSP calculation and alignment consistency**
 - Understanding how multiple HSPs reflect fragmentation.
 - Translating HSP counts into biologically meaningful penalties.

3. **Metadata loading error**
 - Persistent `UnicodeDecodeError (UTF-8)` while loading metadata.
 - Root cause:
   - Metadata file was an Excel (`.xlsx`) file being read using `pd.read_csv()`.

---

## Solutions and Learnings

- Clarified the **biological meaning of each scoring dimension** before finalizing logic.
- Corrected metadata loading by switching to `pd.read_excel()` for Excel files.
- Used ChatGPT selectively as a **learning and optimization aid** for:
- Concept validation
- Code refinement
- Improving clarity and efficiency
- Reinforced the importance of:
- Matching file formats with appropriate parsers
- Designing biologically informed but computationally restrained scoring models

---

## Current Status
- Core scoring pipeline implemented and functional.
- Metadata successfully integrated into biological modifier scoring.
- Final confidence score reflects statistical strength, alignment consistency, and biological plausibility in a balanced manner.
