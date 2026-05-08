# AntiMPmod: Prediction of Antimicrobial Potential of Chemically Modified Peptides

Welcome to the official repository for AntiMPmod, a computational method and web server for predicting the antimicrobial potential of chemically modified peptides from their tertiary structures. This resource is designed to support researchers in peptide therapeutics, antimicrobial drug discovery, and computational chemistry.

**Web Server:** http://webs.iiitd.edu.in/raghava/antimpmod/
**Structure Prediction Server (PEPstrMOD):** http://webs.iiitd.edu.in/raghava/pepstrmod/
**Standalone (Docker):** Pull image `raghavagps/gpsraghava` and run PERL code from the `gpsr` folder

---

## Citation

Agrawal, P., & Raghava, G. P. S. (2018).
Prediction of Antimicrobial Potential of a Chemically Modified Peptide From Its Tertiary Structure.
*Frontiers in Microbiology*, 9:2551.
https://doi.org/10.3389/fmicb.2018.02551

---

## About the Tool

AntiMPmod is the **first method** developed specifically to predict the antimicrobial activity of chemically modified peptides using their 3D tertiary structures. Unlike all previous AMP prediction methods that rely solely on natural amino acid sequences, AntiMPmod leverages structural features extracted via SMILES format — including atom composition, diatom composition, molecular fingerprints, 2D chemical descriptors, and binary profiles — to handle the full diversity of chemical modifications.

The tool integrates data from:

* SATPDB — Structurally Annotated Therapeutic Peptide Database (source of modified AMPs and non-AMPs)
* PEPstrMOD — for tertiary structure prediction of modified peptides up to 25 residues
* OpenBabel — for SMILES format conversion from PDB structures
* PaDEL — for computing molecular descriptors and fingerprints

---

## Key Features

**Curated Dataset of Modified Peptides**

* 948 modified antimicrobial peptides (positive set)
* 931 modified non-antimicrobial peptides (negative set)
* Modifications include: terminus modifications, chemical modifications, D-amino acids, acetylation, amidation, methylation, glycosylation, non-natural residues (ornithine, norleucine, etc.)
* 80/20 split for training and validation

**Novel Structure-Based Feature Extraction**

* Atom Composition — fraction of 8 atom types (C, H, O, N, S, Cl, Br, F); 8-dimensional vector
* Diatom Composition — fraction of 64 atom pairs (CC, CO, CN, etc.); 64-dimensional vector
* 2D Chemical Descriptors — 231 descriptors computed via PaDEL; reduced to 4 via feature selection
* Molecular Fingerprints — 4812 fingerprint features computed via PaDEL (10 fingerprint types)
* Hybrid Features — 2D descriptors + fingerprints (5043 features; reduced to 20 via feature selection)
* Binary Profiles from SMILES — three categories:
  * Only atoms (C, H, O, N, S, F, Cl, Br) — N×8 vector
  * Only symbols (@, +, =, #, [, ], .) — N×7 vector
  * Atoms + symbols combined — N×15 vector

**Multiple Machine Learning Classifiers**

* Support Vector Machine (SVM) — SVMlight with RBF kernel
* Random Forest (RF)
* J48 Decision Tree
* SMO (Sequential Minimal Optimization)
* Naïve Bayes
* Feature selection via WEKA (CfsSubsetEval + Best First search)

**Hard Benchmarking Dataset**

* Mod_AMP_similar — compositionally similar modified AMPs and non-AMPs
* Negative peptides selected by minimum Euclidean distance of diatom composition from positive peptides
* Used to test model robustness on near-identical composition but different activity sequences

**Statistical Validation**

* Mann–Whitney–Wilcoxon non-parametric test used to assess significance of features
* p-value < 0.05 confirmed for most discriminating features

**Web Server Modules**

* Predict — submit PDB structure of modified peptide; get AMP or non-AMP prediction with score
* Download — download datasets used in this study

---

## Overview

AntiMPmod provides structure-based machine learning prediction along with:

* Binary classification of modified AMPs vs. modified non-AMPs
* Atom and diatom composition analysis of chemically modified peptides
* Fingerprint and chemical descriptor-based modeling
* Binary profile generation from SMILES terminal segments
* Hard benchmarking on compositionally similar modified peptide pairs
* Integration with PEPstrMOD for users without existing peptide structures
* Docker-based standalone deployment

---

### Structural Insights from Modified AMPs

Analysis of modified AMP and non-AMP structures revealed:

* **Atom level:** Modified AMPs slightly enriched in C atoms; non-AMPs enriched in S atoms; halogens (F, Cl, Br) absent in both
* **Diatom level:** Diatom `CC` dominant in AMPs; `NC`, `OC`, `CS`, `SC` more abundant in non-AMPs
* **Secondary structure:** Modified AMPs dominated by turns, coils, and extended loop regions (~62.5%), followed by helical content (~36%) and minimal sheet content (~1.5%)

---

### Best Model Performance Summary

| Feature | Best Classifier | Train Acc (%) | Train MCC | Val Acc (%) | Val MCC |
|---------|----------------|--------------|-----------|-------------|---------|
| Atom Composition | SVM | 86.83 | 0.74 | 83.51 | 0.67 |
| Diatom Composition | Random Forest | 89.75 | 0.80 | 87.50 | 0.75 |
| 2D Descriptors (after FS) | SVM | 80.68 | 0.62 | 79.79 | 0.60 |
| **Fingerprints (full)** | **SVM** | **91.62** | **0.84** | **89.89** | **0.80** |
| Binary Profile N100C100 (atoms only) | SVM | 89.84 | 0.80 | 87.37 | 0.75 |
| Binary Profile N200C200 (atoms + symbols) | SVM | 89.35 | 0.79 | 85.86 | 0.72 |

> **Best overall model:** Fingerprints (full, 4812 features) — SVM with accuracy **91.62%** and MCC **0.84** on training; **89.89%** accuracy and MCC **0.80** on validation.

 
### Advantages Over Sequence-Based Methods

| Aspect | Sequence-Based Methods | AntiMPmod |
|--------|----------------------|-----------|
| Chemical modifications | Not supported | Fully supported |
| Feature type | Amino acid / dipeptide composition | Atom composition, fingerprints, SMILES binary profiles |
| Input format | FASTA sequence | PDB tertiary structure |
| Modification types handled | Natural residues only | Terminus, chemical, D-amino acids, non-natural residues |
| 3D structural information | Not used | Fully utilized via PaDEL descriptors |

---

### Limitations

* Requires tertiary structure (PDB format) as input — experimental structure determination is time-consuming and costly
* Computational structure prediction tools (PEPstrMOD, molecular dynamics) have inherent accuracy limitations
* Maximum peptide length of 25 residues for structure prediction via PEPstrMOD
* Post-translational modifications not considered beyond the SATPDB scope
* Cannot predict broad-spectrum activity or toxicity of designed peptides

---

## Applications

* Prediction of antimicrobial activity for chemically modified peptides
* Rational design of modified AMP analogs with enhanced stability and potency
* Screening of peptide libraries containing non-natural and D-amino acid residues
* Drug discovery pipeline for antibiotic-resistant pathogen combat
* Research on modification strategies that enhance antimicrobial properties (acetylation, amidation, glycosylation, halogenation)
* Integration with PEPstrMOD for end-to-end modified peptide design and evaluation

---

## Contact & Authors

**Prof. Gajendra P. S. Raghava**
raghava@iiitd.ac.in
Indraprastha Institute of Information Technology (IIIT Delhi)
New Delhi, India
http://webs.iiitd.edu.in/raghava/

**Piyush Agrawal**
CSIR-Institute of Microbial Technology, Chandigarh, India
Center for Computational Biology, IIIT Delhi, India

Developed at **CSIR-Institute of Microbial Technology (IMTECH), Chandigarh** and **IIIT Delhi, India**

---

## License

This tool is distributed under the terms of the
**Creative Commons Attribution License (CC BY 4.0)**
© 2018 Agrawal and Raghava.
Use, distribution, or reproduction in other forums is permitted provided the original authors and publication are credited.

 
We acknowledge all researchers whose published work on antimicrobial and chemically modified peptides contributed to this dataset, and the developers of SATPDB, PEPstrMOD, PaDEL, and OpenBabel tools used in this study.
