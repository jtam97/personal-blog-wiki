
==**Diffusion models**==

**DDPM** (Denoising Diffusion Probabilistic Models) - The core idea: Learn to reverse gradual noising process
- Forward process: Add gaussian noise over T timesteps until data becomes pure noise
- Reverse process: Train a neural network (usually a U-Net) to predict noise added at each step, then iteratively denoise
	- U-Net - a CNN developed for precise and fast image segmentation, particularly used for biomedical applications
- Training objective: Minimize ||ε − ε_θ(x_t, t)||² - predict noise from noisy sample at timestep t

**KEY INSIGHT** - Never need to sample from a complex distribution directly. You learn small, local denoising steps
**Weakness** - Slow inference - requires ~1000 sequential denoising steps

**DDIM** (Denoising Diffusion Implicit Models) - Fixes DDPM's speed problem by reformulating reverse process
- Reverse process is non-Markovian process - steps no longer sequential and independent
- Can skip timesteps, reducing sampling from ~1000 steps to ~50 with minimal quality loss
- Same training objective but DDIM is purely an inference time improvement
- DDIM is **deterministic** given a starting noise vector - useful for interpolation and editing

**Score matching**
The backbone underlying modern diffusion
- Instead of predicting noise, frame the problem as learning a score function: the log-probability density
- Score-based stochastic differential equations - Generalize diffusion as SDE. Forward process adds noise continuously; reverse SDE uses learned score to denoise
- **Intuition**
	- Imagine like gradient ascent, where you look at the gradients to point you towards the high-probability region (most realistic protein)
	- You can't directly compute gradient because you don't have p(x) - probability distribution over protein backbone coordinates
	- Instead you take a real data point x_0, add gaussian noise to get x_noisy, and then train a network to predict which direction to go to get back x_0

**RFDiffusion**
Landmark application of diffusion to protein design
- Built on top of RoseTTAFold - runs diffusion in the space of protein backbone coordinates
- Noises 3D coordinates (x,y,z of alpha carbons) and learns to denoise them into a valid protein backbone
- Conditioned generation - can fix certain residues (e.g. binding site) and diffuse the rest of the protein. Enables: 
	- Binder design - Design a new protein that physically binds to target protein of interest
	- Motif scaffolding - given an influential motif, build a whole new protein around the motif that holds it in the right shape
	- Symmetric oligomers - generate backbones assembling multiple identical subunits 
- **Why its powerful** - does not require a sequence to start, generates backbone geometry, then sequence design can follow (ProteinMPNN)
	- It basically runs diffusion over only alpha carbon coordinates and backbone torsion angles

- A typical pipeline using RFDiffusion
	1. RFDiffusion to predict backbone geometry
	2. ProteinMPNN to find sequence that actually folds into backbone
	3. AlphaFold2 to validate that the designed sequence folds into the predicted structure

**Simple Analogy**
- RFDiffusion designs the blueprint - shape of the building, where the walls go
- ProteinMPNN chooses the materials for the building - which amino acids
- AlphaFold checks that material is actually CAPABLE of building intended structure


==**Flow matching**== - Continuous normalizing flows
Flow matching learns a vector field that transports samples from a simple gaussian distribution to the data distribution along smooth trajectories
- Advantage over diffusion: straighter trajectories -> fewer NFE (neural function evaluations) at inference
- AlphaFold3 and Boltz-1 uses flow matching over atom coordinates rather than diffusion
	- Sequence generation 
	- Flexible

==**Variational Autoencoders (VAEs)**==
VAES
- Encode sequence into a continuous latent space, and then decode back
- ChemVAE

**Evaluation in Generative Bio Models**
Perplexity
- Measures how well a model predicts held-out sequences
- Lower = better for language model-style sequence models
FID (Frechet Inception Distance)
- Originally for images - compare statistics of generated vs real samples in feature space
- Measure distributional overlap

**==Bio-specific Foundation Models to Know About==**
ESM-2
- Pure protein sequence transformer, trained with MLM
- Converts sequence to embeddings with structure-adjacent information
ESM-3
- Multi-modal - jointly reasons over sequence, structure (tokenized via VQ-VAE on backbone coordinates), and function (GO terms, keywords) 
- Trained with generative masked diffusion approach
- **What it can do**: Can generate sequences _conditioned on structure_, or structures conditioned on function descriptions.
Evo (Arc Institute)
- DNA foundation model
- single-nucleotide tokenization - No byte-pair encoding
- Architecture uses StripedHyena - a hybrid of attention and Hyena (long-convolution) layers
- Trained with CLM (next-nucleotide prediction)
- **What it can do**: generate functional genes, predict mutation effects, do sequence-to-function prediction across the central dogma.
Nucleotide Transformer(InstaDeep/EMBL-EBI)
- Transformer trained on multi-species genomes with 6-mer tokenization
- **What it can do**: chromatin accessibility, splice site prediction, promoter activity.
DNABERT-2
RNA-FM
- Trained on **non-coding RNA sequences** from RNAcentral
- Captures RNA-specific features: secondary structure propensity, RNA family membership, conservation.
