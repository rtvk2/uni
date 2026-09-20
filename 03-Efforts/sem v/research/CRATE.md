
# White-Box Transformers via Sparse Rate Reduction - Detailed Notes

## Metadata
**Paper**: White-Box Transformers via Sparse Rate Reduction
**Authors**: Yaodong Yu, Sam Buchanan, Druv Pai, Tianzhe Chu, Ziyang Wu, Shengbang Tong, Benjamin D. Haeffele, Yi Ma
**Link**: [2306.01129v1.pdf](2306.01129v1.pdf)
**Tags**: #transformer #interpretability #rate-reduction #sparse-coding #white-box

---

## Abstract

### Paragraph 1: Core Thesis
The paper argues that the goal of representation learning is to transform data distributions (sets of tokens) towards a mixture of low-dimensional Gaussian distributions supported on incoherent subspaces. The quality is measured by the **sparse rate reduction** objective function.

> The key insight is that representation learning should compress data into structured, compact representations that can be described by a mixture of subspaces.

### Paragraph 2: Deriving Transformers from Optimization
The paper shows that standard transformer blocks can be derived from alternating optimization on complementary parts of the sparse rate reduction objective:
- **Multi-head self-attention** → gradient descent step to **compress** token sets by minimizing lossy coding rate
- **Multi-layer perceptron (MLP)** → step to **sparsify** token representations

This leads to a family of "white-box" transformer architectures that are mathematically interpretable.

### Paragraph 3: Empirical Results
Despite their simplicity, these networks:
- Successfully optimize the designed objective
- Compress and sparsify representations of large-scale datasets (ImageNet)
- Achieve performance close to engineered transformers like ViT

---

## 1 Introduction

### Paragraph 1: Background on Deep Learning Success
Deep learning has succeeded in processing high-dimensional, multi-modal data by learning effective representations. These representations are:
- **Parsimonious**: structured and compact
- **Useful**: for downstream tasks (classification, segmentation, generation)

The paper aims to unify different approaches to representation learning.

### Paragraph 2: Transformer Models and Self-Attention
Transformers are a popular architecture for learning representations from structured data (text, images, other signals):
- Convert data points into **sets or sequences of tokens**
- Process tokens in a medium-agnostic way
- Self-attention exploits statistical correlations among tokens

**Problem**: Transformers are empirically designed and lack rigorous mathematical interpretation. The relationship between data distribution and learned representation remains a "black box."

### Paragraph 3: Diffusion Models and Denoising
Diffusion models learn data distributions through iterative denoising:
- Start with Gaussian noise
- Iteratively denoise until reaching data distribution
- Key component: **score function** (or optimal denoising function)
- Typically implemented with black-box networks

**Limitations**: No clear correspondence between initial features and data samples; do not offer interpretable representations.

### Paragraph 4: Structure-Seeking Models and Rate Reduction
Two approaches to explicit representation learning:
1. **Model-based**: sparse coding, dictionary learning
2. **Model-free**: contrastive learning, maximal coding rate reduction

**Advantages**: More interpretable; allow designing desired properties
**Disadvantage**: Narrowly defined properties may limit performance on large datasets

### Paragraph 5: Contributions and Outline
The paper proposes a unified framework for designing transformer-like networks that are:
- **Mathematically interpretable** (white-box)
- **High-performing** on real-world tasks

**Approach**: Learn incremental mappings to optimize the sparse rate reduction objective (Equation 1).

### Paragraph 6: Summary of Technical Contributions
1. **Section 2.2**: Shows self-attention emerges from denoising tokens towards subspaces
2. **Section 2.3**: Derives multi-head self-attention as gradient descent on coding rate
3. **Section 2.4**: Shows MLP can be interpreted as sparse coding (ISTA)
4. **Section 2.5**: Creates the **CRATE** architecture (Coding RAtE reduction Transformer)

### Paragraph 7: Significance
The framework makes everything interpretable:
- Objective function
- Architecture
- Learned representation

Experiments (discussed later) show CRATE performs on par with engineered transformers like ViT.

---

## 2 Technical Approach and Justification

### 2.1 Objective and Approach

#### Paragraph 1: Setup Notation
Defines the learning setup:
- **X** ∈ ℝ^(D×N): data source with N tokens
- **Z** ∈ ℝ^(d×N): representations of tokens
- B samples: X₁,...,X_B and Z₁,...,Z_B
- **Z^ℓ**: output of first ℓ layers

#### Paragraph 2: Objective for Learning Structured Representations
Goal: Find feature mapping f: X → Z that transforms input data to a **piecewise linearized and compact** representation.

Target distribution: mixture of K low-dimensional Gaussians:
- Mean: 0 ∈ ℝ^d
- Covariance: Σ_k ⪰ 0
- Support: orthonormal basis U_k ∈ ℝ^(d×p)

**Objective**: Maximize **rate reduction** ΔR(Z; U_[K]) = R(Z) - R^c(Z; U_[K])
- R(Z): coding rate of the whole representation
- R^c(Z; U_[K]): coding rate relative to subspaces

**Sparsity**: Add ℓ₀ norm penalty to ensure representations are sparse with respect to standard coordinates.

#### Paragraph 3: Unified Objective (Equation 1)
The combined objective is:
```
max_{f∈F} E_Z [ΔR(Z; U_[K]) - λ||Z||₀]
```
= max_{f∈F} E_Z [R(Z) - R^c(Z; U_[K]) - λ||Z||₀] s.t. Z = f(X)

This is the **sparse rate reduction** objective.

#### Paragraph 4: White-Box Architecture via Unrolled Optimization
The global transformation f is realized through multiple incremental operations:
```
f: X → Z⁰ → Z¹ → ... → Z^ℓ → Z^(ℓ+1) → ... → Z^L = Z
```
- f⁰: pre-processing mapping from input tokens to token representations
- Each layer f^ℓ optimizes the objective locally

**Key innovation**: Unlike ReduNet, CRATE explicitly models the distribution of Z^ℓ at each layer (e.g., as a mixture of subspaces) and learns model parameters from data via backpropagation.

#### Paragraph 5: Two-Step Alternating Minimization
Each layer f^ℓ consists of two steps:
1. **Compression**: Gradient descent to minimize coding rate R^c(Z; U_[K])
2. **Sparsification**: Proximal gradient step on [λ||Z||₀ - R(Z)]

Both steps are applied incrementally and repeatedly across layers.

---

### 2.2 Self-Attention via Denoising Tokens Towards Multiple Subspaces

#### Paragraph 1: Motivation
This section studies an idealized model that reveals why self-attention-like operators arise naturally. The model assumes:
- Single token (N = 1) drawn from mixture of low-dimensional Gaussians
- Corrupted with additive Gaussian noise: x = z + σw

Goal: Transform noisy token x to the mixture of low-dimensional Gaussians z.

#### Paragraph 2: Incremental Denoising
Reasoning inductively: if z^ℓ is a noisy token at noise level σ^ℓ, produce z^(ℓ+1) by denoising.

Optimal mean-square estimate: E[z | z^ℓ]
Variational characterization: argmin_f E[||f(z + σ^ℓ w) - z||₂²]

This characterizes the next stage of (2) in terms of an optimization objective based on a local signal model.

#### Paragraph 3: Tweedie's Formula
Tweedie's formula expresses the optimal representation in closed form:
```
z^(ℓ+1) = z^ℓ + (σ^ℓ)² ∇_x log q^ℓ(z^ℓ)
```
where q^ℓ is the density of z^ℓ.

This gives the representation as an incremental perturbation to the noisy distribution.

#### Paragraph 4: Closed-Form Expression for Self-Attention
For the mixture of Gaussians model, the score function can be calculated in closed form (Equation 6):
```
z^(ℓ+1) ≈ [U₁,...,U_K] [diag(softmax(...)) ⊗ I_p] [U₁*z^ℓ; ...; U_K*z^ℓ]
```

**Key insight**: This resembles a self-attention layer with:
- K heads
- Sequence length N = 1
- Query-key-value replaced by linear projection U_k*z^ℓ

**Interpretation**: Gaussian denoising against a mixture of subspaces leads to self-attention-type layers.

---

### 2.3 Self-Attention via Compressing Token Sets Through Optimizing Rate Reduction

#### Paragraph 1: Challenges
The previous section assumes known mixture parameters, but in practice:
- Parameters must be estimated from finite samples
- Tokens are not i.i.d. - joint distributions encode rich information (co-occurrences)
- Need to compress/transform sets of tokens together

#### Paragraph 2: Coding Rate as Compactness Measure
For a set of tokens Z ∈ ℝ^(d×N), the (lossy) coding rate up to precision ε > 0 is:
```
R(Z) = ½ log det(I + d/(Nε²) Z*Z)
```
For a single zero-mean Gaussian distribution.

#### Paragraph 3: Multi-Modal Data and Subspace Mixtures
For multi-modal data (multiple classes or patches), the distribution is a mixture of K subspaces with bases U_k ∈ ℝ^(d×p).

The coding rate relative to subspaces:
```
R^c(Z; U_[K]) = Σ_{k=1}^K ½ log det(I + p/(Nε²) (U_k*Z)*(U_k*Z))
```

#### Paragraph 4: Gradient of the Coding Rate
The gradient of R^c with respect to Z is:
```
∇_Z R^c(Z; U_[K]) = p/(Nε²) Σ_k U_k U_k*Z (I + p/(Nε²)(U_k*Z)*(U_k*Z))^(-1)
```

#### Paragraph 5: Approximating the Gradient Step
Using a von Neumann expansion and softmax for subspace membership, the gradient step becomes:
```
Z^(ℓ+1/2) ≈ (1 - κ·p/(Nε²))Z^ℓ + κ·p/(Nε²)·MSSA(Z^ℓ | U_[K])
```

Where MSSA is the **Multi-Head Subspace Self-Attention** operator:
```
SSA(Z | U_k) = (U_k*Z) softmax((U_k*Z)*(U_k*Z))
MSSA(Z | U_[K]) = [U₁,...,U_K] [SSA(Z|U₁); ...; SSA(Z|U_K)]
```

**Key insight**: This resembles standard transformer attention with:
- V = K = Q = U_k* (all set to subspace basis)
- Skip connection implementation

#### Paragraph 6: Connection to Ideal Denoiser
When N = 1 and taking an aggressive gradient step (κ = 1), the MSSA operator becomes the ideal denoiser from Equation 6. This provides two related interpretations:
1. **Denoising** against a mixture of subspaces
2. **Compression** through optimizing coding rate

---

### 2.4 MLP via Iterative Shrinkage-Thresholding Algorithms (ISTA) for Sparse Coding

#### Paragraph 1: Sparsification Goal
After compression, the remaining terms in (1) serve to sparsify the representations:
```
max_Z [R(Z) - λ||Z||₀] = min_Z [λ||Z||₀ - ½ log det(I + d/(Nε²) Z*Z)]
```
- R(Z): promotes diversity and non-collapse
- ||Z||₀: promotes sparsity

#### Paragraph 2: Simplifying the Expansion Term
Prior work struggled with gradient ∇_Z R(Z) due to matrix inverse scalability.

The paper takes a different approach:
- Introduce an incoherent/orthogonal dictionary D ∈ ℝ^(d×d)
- Sparsify intermediate iterates: Z^(ℓ+1/2) = D Z^(ℓ+1) where Z^(ℓ+1) is sparse
- Since D is orthogonal, R(Z^(ℓ+1)) ≈ R(D Z^(ℓ+1)) = R(Z^(ℓ+1/2))

#### Paragraph 3: Sparse Coding as LASSO
This becomes a sparse representation program:
```
Z^(ℓ+1) = argmin_Z ||Z||₀ subject to Z^(ℓ+1/2) = D Z
```
Relaxed to LASSO:
```
Z^(ℓ+1) = argmin_Z [λ||Z||₁ + ||Z^(ℓ+1/2) - D Z||_F²]
```

#### Paragraph 4: Non-Negative Constraint
Adding a non-negative constraint (motivated by Sun et al. and Zarka et al.):
```
Z^(ℓ+1) = argmin_{Z≥0} [λ||Z||₁ + ||Z^(ℓ+1/2) - D Z||_F²]
```

#### Paragraph 5: ISTA Update
Solving with one step of proximal gradient descent (ISTA):
```
Z^(ℓ+1) = ReLU(Z^(ℓ+1/2) + ηD*(Z^(ℓ+1/2) - D Z^(ℓ+1/2)) - ηλ1)
```
This is the **ISTA** block.

**Key insight**: The MLP layer can be interpreted as a sparse coding step.

---

### 2.5 The Overall White-Box CRATE Architecture

#### Paragraph 1: Combining the Two Steps
Combining the two steps:
1. **Compression**: Multi-head subspace self-attention (MSSA)
2. **Sparsification**: ISTA block

Results in the CRATE layer:
```
Z^(ℓ+1/2) = Z^ℓ + MSSA(Z^ℓ | U_[K]^ℓ)
Z^(ℓ+1) = ISTA(Z^(ℓ+1/2) | D^ℓ)
```

#### Paragraph 2: Full Architecture
Composing multiple layers following the incremental construction in (2) gives the full CRATE architecture that transforms data tokens towards a compact and sparse union of incoherent subspaces.

#### Paragraph 3: Layer-Dependent Parameters
Parameters (U_[K]^ℓ) and (D^ℓ) are **layer-dependent** - learned separately at each layer via backpropagation.

**Interpretation**:
- U_[K]^ℓ: incoherent bases for supporting subspaces at layer ℓ
- D^ℓ: sparsifying dictionary at layer ℓ

#### Paragraph 4: Distinction from Prior Work
This parameterization distinguishes CRATE from previous unrolled optimization approaches like ReduNet:
- Forward pass: denoise/compress/sparsify input using local signal models
- Backward pass: learn local signal models from data via supervision

#### Paragraph 5: Simplicity and Interpretability
CRATE uses the simplest possible construction choices, but is:
- Fully mathematically interpretable
- Connects to existing transformer models
- Achieves competitive results on real-world datasets (as discussed in Section 3)

---

## Key Equations Reference

### Equation 1: Sparse Rate Reduction Objective
```
max_{f∈F} E_Z [R(Z) - R^c(Z; U_[K]) - λ||Z||₀]
```

### Equation 7: Coding Rate
```
R(Z) = ½ log det(I + d/(Nε²) Z*Z)
```

### Equation 8: Coding Rate Relative to Subspaces
```
R^c(Z; U_[K]) = Σ_k ½ log det(I + p/(Nε²)(U_k*Z)*(U_k*Z))
```

### Equation 10: MSSA Update
```
Z^(ℓ+1/2) = (1 - κ·p/(Nε²))Z^ℓ + κ·p/(Nε²)·MSSA(Z^ℓ | U_[K])
```

### Equation 12: MSSA Definition
```
SSA(Z | U_k) = (U_k*Z) softmax((U_k*Z)*(U_k*Z))
MSSA(Z | U_[K]) = [U₁,...,U_K] [SSA(Z|U₁); ...; SSA(Z|U_K)]
```

### Equation 17: ISTA Update
```
Z^(ℓ+1) = ReLU(Z^(ℓ+1/2) + ηD*(Z^(ℓ+1/2) - D Z^(ℓ+1/2)) - ηλ1)
```

### Equation 18: CRATE Layer
```
Z^(ℓ+1/2) = Z^ℓ + MSSA(Z^ℓ | U_[K]^ℓ)
Z^(ℓ+1) = ISTA(Z^(ℓ+1/2) | D^ℓ)
```

---

## Key Insights

1. **Self-attention as compression**: Multi-head self-attention can be interpreted as gradient descent on coding rate
2. **MLP as sparse coding**: MLP can be interpreted as ISTA for sparse coding
3. **Layer-dependent parameters**: Different layers learn different subspace bases and dictionaries
4. **Interpretability**: CRATE is a "white-box" where each layer has a clear mathematical purpose
5. **Unified framework**: Connects denoising, compression, and sparsification

---

## Architecture Components

### MSSA Block (Compression)
- Projects tokens onto K subspaces
- Computes similarity via auto-correlation
- Uses softmax for membership distribution
- Aggregates heads with learned weight

### ISTA Block (Sparsification)
- Sparse codes representation using dictionary D
- Non-negative ReLU activation
- Proximal gradient descent step
- Promotes sparsity while maintaining diversity
