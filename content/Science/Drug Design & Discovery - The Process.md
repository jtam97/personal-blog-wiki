==**The Drug Discovery Pipeline**==
1. **Target Identification and Validation** - Before designing a drug, you need to find a target

What makes a good target?
- Disease relevance - genetic evidence that the target is causally linked to disease
- Druggability - Can a small molecule, biologic, or RNA therapy actually target it?
- Selectivity potential - Can you hit this target without affecting other members that would cause toxicity?
- Biomarker availability - Can you measure target engagement in a patient?
- Expression pattern - Is it expressed in a specific part of the body but nowhere else? E.g only expressed in disease tissue.

Validation Approaches
- Genetic: siRNA/shRNA knockdown, CRISPR knockout, patient genetics
- Pharmacological: tool compounds that inhibit target
- Disease models: animal knockouts, organoids, patient-derived cells


2. **Hit discovery** - HTS, virtual screening, fragment-based design

- High throughput screening (HTS) - physically test thousands to millions of compounds against a target in automated assays
	- Produces "hits" - compounds that show activity above a threshold
- Virtual screening - computationally dock large libraries against a target structure
- Fragment-based design - screen very small molecules that bind weakly but efficiently
	- Find two fragments that bind adjacent sites, and link them into a single large molecule with high affinity

3. **Lead optimization** - ADMET properties and medicinal chemistry
- Lead optimization is the iterative process of taking a hit compound and making it better across multiple axes simultaneously
	- **A central challenge of drug discovery**
- Absorption
- Distribution
- Metabolism
- Excretion
- Toxicity

4. **Preclinical -> IND -> Phase I/II/III**
- Preclinical is in-vitro and in-vivo testing
- IND (investigational New Drug) Filing
	- Submit to FDA to request permission to begin human trials
- Phase I - Safety
	- First-in-human tests, typically healthy volunteers
	- Goal: is it safe?
- Phase II - Efficacy signals
	- Hundreds of patients with target disease
	- Goal: does it work? Whats the right dose? What are common side effects?
- Phase III - Confirmation
	- Thousands of patients, randomized controlled trial against standard of care or placebo
	- Goal: statistically confirm efficacy and characterize safety at scale


==**Therapeutic Modalities**==

**mRNA therapeutics**
- mRNA delivers instructions to the cell to produce a desired protein
	- cell's ribosomes read message and make proteins - no DNA involved and transient

Anatomy of mRNA
5' Cap - 5' UTR - Start Codon - CDS (Coding sequence) - Stop Codon - 3' UTR - Poly-A Tail
- **Therapeutic mRNA uses pseudouridine modification** - dramatically reduce innate immune activation
	- not optional: unmodified mRNA is too immunogenic and too poorly translated for most therapeutic uses
- LNP (Lipid Nanoparticle) delivery

**siRNA (Small Interfering RNA)**
**ASOs (Antisense Oligonucleotides)**
**Aptamers**
**SELEX (Systematic Evolution of Ligands by Exponential enrichment)** 
**Antibodies**


==**Computational Drug Design**==
1. Structure-based vs Ligand-based Drug Design
	- Structure-based requires a 3D structure of the target - design molecules that fit geometrically and chemically
	- Ligand-based does not require target structure - only a set of known molecules - what 3D features do all actives share?
2. Molecular Docking
	- Computationally predict how a small molecule (ligand) binds to a target (receptor) - finds the lowest-energy binding pose and estimates binding affinity
	- Typically scoring functions
		- Force-field based
		- Empirical
		- Knowledge-based
		- ML-based
3. Molecular dynamics
	- Simulate the physical motion of atoms over time by numerically integrating Newton's equation of motion - captures *dynamic* behavior of molecules that docking misses
	- Captures flexibility, solvation, entropic effects, binding kinetics, allosteric effects
4. Free energy perturbation
	- thermodynamic method to calculate the binding free energy difference between two related compounds. Gold standard for affinity prediction in lead optimization.
5. ADMET prediction methods


==**Sequence Optimization for Therapeutics**==
**Codon optimization - The degeneracy problem:** Most amino acids are encoded by multiple codons (synonyms). Which codon you choose doesn't change the protein sequence, but it dramatically affects:
1. **Translational speed:** Rare codons slow ribosome elongation (lack of cognate tRNA). Sometimes this is good (folding time), but generally slows expression
2. **Translational accuracy:** Rare codons increase misincorporation rate
3. **mRNA stability:** Codon usage affects mRNA folding, which affects degradation rate
4. **Immunogenicity:** CpG dinucleotides in DNA and dsRNA structures in mRNA can trigger innate immune sensors