# Day 9 — Gene Grouping, Reconstruction Rules, and Gene-Level Confidence Aggregation.
DATE - 08-01-2026
TIME - ~4HRS

## What Do We Mean by a Gene Group?

A **gene group** represents a set of BLAST alignments that collectively describe a **single candidate AMR gene** within a query sequence.

In simple terms, a gene group is a collection of BLAST hits that share common identifying and positional characteristics—most importantly the same **query sequence ID (`qseqid`)** and **subject sequence ID (`sseqid`)**. Multiple hits for the same gene–database pair can arise due to:

- Gene fragmentation across contigs  
- Interruptions caused by low-complexity regions  
- Assembly or sequencing artifacts  

Treating these alignments as independent hits would incorrectly inflate gene counts and distort confidence scoring. Therefore, **explicit grouping criteria** are required to determine whether multiple alignments should be reconstructed and evaluated as a single gene.

### Spatial Basis for Grouping

Two or more hits may be included in a single gene group if:

- They share the same `qseqid` and `sseqid`
- Their query coordinates (`qstart`, `qend`) overlap or are separated by an allowable gap

### Why Gene Grouping Matters

This grouping strategy enables:

- Accurate estimation of HSP count per gene  
- Correct calculation of coverage and fragmentation  
- Application of fragment tolerance metadata  
- Prevention of false confidence inflation due to duplicated fragment hits  

### Summary Definition

A **gene group** is defined by:
- **Identity:** same `sseqid`  
- **Location:** same `qseqid`  
- **Spatial continuity:** overlapping or adjacent query coordinates  

This definition provides a biologically sound and computationally robust basis for fragment-aware AMR gene scoring.

---

## Reconstruction Rules

Reconstruction rules are applied to ensure that only **biologically plausible fragment sets** influence confidence scoring. The goal is not to blindly assemble fragments, but to determine whether multiple alignments represent a legitimate gene or unrelated noise.

These rules preserve robustness, prevent incorrect merging, and maintain interpretability.

---

### Rule 1 — Minimum Combined Coverage

**Definition**

Minimum combined coverage is calculated as:

combined_coverage =
(sum of all non-overlapping aligned lengths) / reference_gene_length

For the combined gene length the aligned length of the fragments to be considered having maximum overlapping of 30-50 bp and the max gap of 150bp, if there is overlapping for example fragments of 1-400 HIT 1 and 350-500 HIT 2, then the 
total covered = union of [1–500].

**Threshold**

- Combined coverage must be **≥ 80%**

**Rationale**

AMR genes may tolerate minor truncations without losing detection confidence. Coverage below this threshold often represents:

- Isolated conserved domains  
- Spurious partial hits  
- Non-functional remnants  

This rule ensures reconstruction only when most of the gene is represented, even if fragmented.

---

### Rule 2 — Maximum Allowed Gap Between Fragments

**Definition**

The gap between two consecutive fragments is calculated as:

gap = | previous_fragment.qend − next_fragment.qstart |

**Threshold**

- Maximum allowed gap ≤ **150 bp**  
  **OR**  
- Approximately **10–15% of the reference gene length**

**Rationale**

Small gaps may arise from:

- Assembly breaks  
- Low-complexity masking  
- Sequencing artifacts  

Large gaps suggest:

- Independent alignments  
- Insertions or unrelated genomic regions  

This rule prevents biologically implausible long-range reconstruction.

---

### Rule 3 — Strand Consistency

**Definition**

All fragments belonging to a gene group must align to the **same strand**.

**Allowed Cases**
- All fragments on the `+` strand  
- All fragments on the `−` strand  

**Disallowed Case**
- Mixed strand alignments within the same gene group

Rule: If fragments are on different contigs, allow reconstruction only if:
    - They map to distinct, non-overlapping parts of the reference gene, and
    - They are separated by less than 150 bp when projected onto a pseudo-genome coordinate, or
    - When assembled order is ambiguous, stash as “candidate reconstruction”.
 
**Rationale**

A single gene cannot biologically span both strands. Mixed-strand hits strongly indicate:

- Spurious alignments  
- Paralogous domains  
- Repeat artifacts  

This rule significantly improves reconstruction specificity.

---

### Rule 4 — Fragment Order Consistency

**Definition**

Fragments must appear in a **monotonic order** along the query sequence.

**Valid Example**
Fragment 1: qstart = 100, qend = 400
Fragment 2: qstart = 410, qend = 800

**Invalid Example**
Fragment 1: qstart = 500, qend = 800
Fragment 2: qstart = 100, qend = 300

Also the max allowed gap, as mentioned earlier should be lower or equal to 150bp to be considered for reconstruction and coverage calculation. 

**Rationale**

Reordering implies:

- Non-contiguous gene structure  
- Independent alignment artifacts  

This rule ensures spatial coherence of reconstructed genes.

---

### Rule 5 — Non-Overlapping Contribution

**Definition**

Overlapping alignment regions are counted **only once** when calculating coverage.

**Rationale**

This prevents artificial inflation of coverage. Overlapping HSPs often represent:

- Redundant alignments  
- Alternative scoring paths  

Coverage must reflect the **unique gene span**, not alignment redundancy.

---

## Why These Rules Matter

Together, these constraints enable:

- Fair treatment of fragmented but valid genes  
- Prevention of false gene inflation  
- Accurate coverage and HSP-based penalties  
- Reliable application of fragment tolerance metadata  

They form the logical foundation for deterministic and biological scoring layers without introducing computationally expensive reconstruction.

---

## Aggregation Strategy for Reconstructed Genes

When multiple BLAST alignments are reconstructed into a single gene group, fragment-level scores must be aggregated into a **gene-level confidence score**.

The aggregation strategy must:
- Preserve strong evidence  
- Penalize structural inconsistency  
- Avoid artificial inflation due to fragmentation  

Each scoring layer captures a different dimension of evidence and therefore requires a distinct aggregation approach.

---

### 1. Aggregating Statistical Scores (S)

**Method: Coverage-Weighted Maximum**

S_group = max(S_i) × combined_coverage

Where:
- `S_i` = statistical score of fragment *i*
- `combined_coverage` = non-overlapping coverage fraction

**Rationale**

- Maximum preserves the strongest biological signal  
- Coverage weighting ensures:
  - Fragmentation cannot inflate confidence  
  - Partial evidence is appropriately discounted  

---

### 2. Aggregating Deterministic Scores (D)

**Method: Minimum Score Selection**

D_group = min(D_i)

**Rationale**

- Structural weaknesses cap confidence  
- Fragmentation is never rewarded  
- Ensures that the weakest structural constraint dominates

---

### 3. Aggregating Biological Modifiers (B)

**Method: Coverage-Weighted Mean with Floor**

B_group =
(Σ (B_i × coverage_i)) / (Σ coverage_i)

With:
- A lower bound (floor), e.g., **B_group ≥ 0.8**

**Rationale**

Biological modifiers represent soft plausibility adjustments. They should:

- Reflect overall gene behavior  
- Not be dominated by small fragments  

Coverage weighting ensures larger, more informative fragments contribute more meaningfully.

---

## Final Gene-Level Score Calculation

After aggregation:

Final_gene_score = S_group × D_group × B_group

This preserves the original gated multiplicative design while extending it to reconstructed genes.

---

## Can Multiple Weak Fragments Become One Strong Gene?

**No.**

### Enforced Safeguards

A reconstructed gene may be classified as **High confidence** only if:

- At least one fragment has a strong statistical signal  
- Reconstruction constraints are satisfied:
  - ≥ 80% combined coverage  
  - Acceptable gap sizes  
  - Structural coherence  
- Deterministic score does not cap confidence  
- Biological modifiers do not strongly penalize plausibility  

Fragment aggregation refines evidence but **cannot create strength**.

---

## Gene-Level Confidence Classes

| Final Gene Score | Classification | Interpretation |
|------------------|---------------|----------------|
| ≥ 0.80 | High | Strong, coherent evidence for a valid AMR gene |
| 0.50 – 0.79 | Medium | Partial but biologically plausible gene presence |
| < 0.50 | Low | Weak or likely spurious detection |

These thresholds mirror fragment-level interpretation but are applied **once at the gene level**, ensuring consistency and robustness.

---
Recon_stat — Weighted Statistical Score (Fragment Reconstruction)

This section describes how fragment-level hits are reconstructed into a gene-level confidence score using weighted statistical, structural, and biological evidence.

1. Example Scenario

Two reconstructed fragments (A and B) map to the same reference gene.

Reference gene length: 1000 bp

Fragment Details
Fragment	Reference Position	Effective Coverage	Scores (S, D, B)
A	1–400	400 bp	S₁ = 0.9, D₁ = 0.8, B₁ = 0.95
B	380–800	400 bp*	S₂ = 0.8, D₂ = 0.7, B₂ = 0.9

* Fragment B overlaps Fragment A by 20 bp.
This overlapping region is redundant and not counted twice.

Fragment Reconstruction Rules Satisfied

Allowed overlap

Minimum gap constraint

Same sseqid (reference gene)

2. Step 1 — Union Coverage
Reconstructed Union Span
1–800 bp → 800 bp total coverage

Gene Coverage Fraction
Coverage fraction
=
800
1000
=
0.8
Coverage fraction=
1000
800
	​

=0.8
3. Step 2 — Recon_stat (Weighted Statistical Score)

Each fragment contributes proportionally to its unique, non-overlapping coverage.

Formula
Recon_stat
=
(
400
800
×
0.9
)
+
(
400
800
×
0.8
)
Recon_stat=(
800
400
	​

×0.9)+(
800
400
	​

×0.8)
Result
Recon_stat
=
0.85
Recon_stat=0.85
Interpretation

First 400 bp contribute using S₁

Second non-redundant 400 bp contribute using S₂

Overlapping bases are not double-counted

4. Step 3 — Recon_det (Structural Integrity Score)

Structural integrity is constrained by the weakest essential fragment.

Formula
Recon_det
=
min
⁡
(
0.8
,
  
0.7
)
Recon_det=min(0.8,0.7)
Result
Recon_det
=
0.7
Recon_det=0.7
Rationale

A gene is only as intact as its weakest region

Strong regions cannot compensate for structural failure

Core principle preserved:

Strong evidence cannot rescue broken structure.

5. Step 4 — Recon_bio (Biological Plausibility)

Recon_bio reflects biological context and reconstructed coverage.

Example Value
Recon_bio
=
0.95
Recon_bio=0.95
6. Step 5 — Final Reconstruction Confidence
Formula
Final_recon_conf
=
Recon_stat
×
Recon_det
×
Recon_bio
Final_recon_conf=Recon_stat×Recon_det×Recon_bio
Calculation
=
0.85
×
0.7
×
0.95
=
0.565
=0.85×0.7×0.95=0.565
7. Final Interpretation

✔ Convincing evidence for AMR gene presence

⚠ Gene is fragmented and structurally incomplete

🔶 Overall confidence is moderate, not high

8. Key Guarantees of This Framework

No score inflation from overlapping fragments

Structural weaknesses are explicitly penalized

Partial genes are distinguished from intact genes
