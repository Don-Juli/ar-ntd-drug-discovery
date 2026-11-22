# ar-ntd-drug-discovery



An ensemble-aware, volatility-filtered computational pipeline designed for **Androgen Receptor N-Terminal Domain (AR-NTD)** targeted drug discovery. 

Because the AR-NTD is an intrinsically disordered region (IDR), traditional static structure-based drug design is challenging. This pipeline implements a five-phase workflow to identify transiently structured pockets (MoRFs) in the Tau-1/AF-1 region and screen drug-like candidates against flexible conformational ensembles.

---

## 🧬 Pipeline Architecture

### Phase 1: Genomic Mining & Dual Disorder / MoRF Profiling
* **UniProt API Query:** Dynamically retrieves the official FASTA sequence and metadata for the Human Androgen Receptor (`P10275`) via the UniProtKB REST API (`urllib.request`), with a built-in canonical sequence fallback.
* **Sub-Domain Selection:** Isolates the **Tau-1/AF-1 transactivation sub-domain** (residues `101–370`).
* **Sliding Window Hydrophobicity & Disorder:** Uses a 9-residue sliding window to compute local Kyte-Doolittle hydrophobicity (`> 0.35` MoRF pocket threshold) alongside an IDR propensity score.

### Phase 2: Scaffold Physicochemical Filtering, QED & BBB Permeability
* **RDKit Descriptor Profiling:** Evaluates lead candidate scaffolds for Molecular Weight, LogP, HBD/HBA, TPSA, ESOL aqueous solubility (`LogS`), Lipinski's Rule of 5, Blood-Brain Barrier (BBB) permeability, and Quantitative Estimate of Drug-likeness (QED).

### Phases 3 & 4: Virtual Library Screening & Toxicophore Filtering
* **Morgan Fingerprinting:** Uses RDKit's `rdFingerprintGenerator` (`radius=2`, `2048-bit`) to rank virtual analogs via Tanimoto Similarity (`>= 0.35` cutoff).
* **Multi-Gate Toxicophore Filtering:** Screens analogs through RDKit `FilterCatalog` against **PAINS**, **BRENK**, and **NIH** alert catalogs. Only candidates with zero alerts advance.

### Phase 5: Bi-Directional Ensemble Generation
* **3D Backbone Modeling:** Fetches AlphaFold $C_\alpha$ coordinates or generates a 5-state self-avoiding elastic network ensemble ($3.8\text{ \AA}$ $C_\alpha\text{–}C_\alpha$ spacing) to capture IDR conformational volatility.

---

## 📊 Generated Outputs (`ar_ntd_outputs/`)

Running the notebook generates three interactive visualizations and seven disk assets inside `ar_ntd_outputs/`:

* `tau1_disorder_profile.csv` — Residue-level Kyte-Doolittle hydrophobicity and disorder probability scores across Tau-1.
* `virtual_screening_results.csv` — Candidate IDs, SMILES strings, Tanimoto similarity scores, and QED metrics for surviving hits.
* `conformer_state_1.xyz` to `conformer_state_5.xyz` — 3D $C_\alpha$ backbone coordinates for each conformational ensemble state (compatible with PyMOL, ChimeraX, and VMD).

---




