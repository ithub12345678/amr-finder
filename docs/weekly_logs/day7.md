# Day 7 Log

## Topic: Biological Modifiers – Fragment Tolerance & Modifying Protein Classification  
**Date:** 24/12/25
**Time Spent:** ~3–4 hours  

---

## Objective
To design a **practical, biologically defensible framework** for incorporating fragment tolerance as a biological context modifier in the AMR confidence scoring pipeline, without relying on extensive gene-by-gene literature review.

---

## Tasks Completed

### 1. Defined Fragment Tolerance as a Gene-Level Property
- Established that **fragment tolerance is not an alignment property**, but a **biological attribute of the AMR gene**
- Decoupled:
  - *Fragmentation detection* → inferred from alignment patterns
  - *Fragment tolerance* → encoded as AMR gene metadata
- Confirmed alignment with practices used in CARD, ResFinder, and AMRFinderPlus

---

### 2. Designed a Minimal Decision Framework for Fragment Tolerance
Implemented a **stepwise inference guide** based on absolute minimum biological checks:

1. Resistance mechanism (enzymatic vs non-enzymatic)
2. Protein functional class (enzyme, transporter, regulator, mobility protein)
3. Structural requirements for activity
4. Protein size and integrity dependency

This allowed fragment tolerance to be inferred **systematically and reproducibly**, rather than heuristically per gene.

---

### 3. Classified Modifying Proteins
Formalized classification of modifying proteins into:

- **Antibiotic-modifying enzymes**
  - e.g., β-lactamases, aminoglycoside-modifying enzymes
- **Target-modifying enzymes**
  - e.g., rRNA methyltransferases (erm family)

Clearly distinguished these from:
- Target protection proteins (e.g., qnr)
- Efflux pumps
- Regulatory and mobility genes

This classification directly informed fragment tolerance decisions.

---

### 4. Assigned Tool-Ready Fragment Tolerance Rules
Mapped resistance mechanisms to default parameters:

- Enzymatic modification → fragment tolerant
- Target modification → fragment tolerant
- Target protection → fragment intolerant
- Efflux, regulatory, mobility genes → fragment intolerant

Defined **default minimum coverage thresholds** based on mechanism class, enabling direct encoding into metadata tables.

---

### 5. Integrated Fragment Tolerance into Scoring Architecture
Designed fragment tolerance as a **biological modifier layer**, applied after:

1. Statistical scoring
2. Deterministic validation

Final confidence score formulation:


This ensured that biologically implausible fragmented hits are penalized even if statistical alignment is strong.

---

## Key Learnings

- Fragment tolerance must be inferred from **gene biology**, not alignment statistics alone
- Enzymatic resistance genes are generally tolerant to partial sequences
- Non-enzymatic resistance mechanisms require near-complete gene integrity
- Encoding biological assumptions explicitly improves transparency and interpretability
- A minimal, rule-based system is sufficient for a practical AMR detection tool

---

## Deliverables Created

- Fragment tolerance inference checklist
- Modifying protein classification schema
- Tool-ready fragment tolerance and minimum coverage rules
- Documentation-ready biological justification for scoring design

---

## Next Steps

- Encode fragment tolerance rules into AMR gene metadata table
- Implement automated inference based on resistance mechanism labels
- Extend biological modifiers to include:
  - ORF completeness
  - Conserved motif presence
  - Operon or plasmid context (future scope)
- Update `design.md` with biological modifier rationale

---
