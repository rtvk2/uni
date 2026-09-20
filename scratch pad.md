
behavioral research is an umbrella term in research of social and behavioral sciences- health policy, social work, cognitive science-  that deals with human behavior

three ways to define it:
way to examine and understand individual and social behavior through measurement and interpretation.
study of observable behavior and inferred mental processes -> think feel decision making, perception
uses both qualitative and quantitative methods to measure and understand human behavior




## Key Terms:

### Page 1

- **White-box vs. Black-box**: A black-box model (e.g., standard ViT) works but its internal logic isn't understood. A white-box model is mathematically transparent — each layer has a proven, specific function.
- **Sparse Rate Reduction**: The paper's unified objective, combining Rate Reduction (compressing data efficiently into low-dimensional subspaces) and Sparsity (representations with mostly zero entries).
- **Mixture of low-dimensional Gaussian distributions**: The assumed ideal structure of a good representation — distinct "blobs" lying on flat, low-dimensional subspaces rather than filling the full space.
- **Tokens**: Small patches (of an image, sentence, etc.) that the network processes as a set.

### Page 2

- **Score function (diffusion)**: The mathematical guide telling a diffusion model which direction to move to remove noise; usually a black-box neural approximation.
- **Unrolling optimization**: Turning each iteration of an iterative algorithm into a single neural network layer, making the network mathematically transparent.
- **Pretext task (contrastive learning)**: An auxiliary task (e.g., "are these two images the same object?") used to learn representations without manual labels.
- **Parsimonious representation**: A representation that is maximally compact and uses the fewest components to describe the data.

### Page 3

- **Piecewise linearized representation**: Flattening curvy, complex data into a set of glued-together flat (linear) subspaces for easier processing.
- **Incoherent subspaces**: Subspaces that are as orthogonal (perpendicular) to each other as possible, so different clusters/classes are clearly separated.
- **Rotation invariance vs. Sparsity**: Rate reduction alone is invariant to rotation of the data; sparsity is added as a penalty to lock the representation into a fixed, useful coordinate system.
- **Lossy coding rates (R and Rᶜ)**: R = bits needed to encode the whole token set; Rᶜ = bits needed given known subspace/class membership. Maximizing R − Rᶜ = better subspace structure.

### Page 4

- **ℓ₀ norm (‖Z‖₀)**: Counts non-zero entries in Z; promoting this (minimizing it) forces sparsity.
- **Unrolled optimization**: An iterative algorithm converted into a network where each layer performs one step.
- **ReduNet**: A prior white-box rate-reduction network; used fixed, global subspace parameters (unlike CRATE's per-layer learned parameters).
- **Conditional expectation E[z|zₗ]**: The statistically optimal (MSE-minimizing) estimate of a clean signal given a noisy observation; used as the gold standard for denoising.

### Page 5

- **Tweedie's formula**: States that the optimal denoised estimate = noisy input + gradient of the log-density (score function) of the noisy distribution.
- **Score function (∇ₓ log q(x))**: Gradient of the log-likelihood; points toward increasing probability of the clean signal. Central to diffusion models too.
- **Kronecker product (x)**: A matrix operation combining softmax subspace-assignment weights with an identity matrix to route tokens to the correct subspace.
- **Lossy coding rate R(Z)**: Measures the "volume"/spread of data in high-dimensional space — how many bits needed to store Z within precision ε.
- **Quantization precision (ε)**: The allowed compression error; larger ε means more aggressive (lossier) compression.

### Page 6

- **Conditional coding rate Rᶜ (Eq. 8)**: Sum of per-subspace coding rates — bits needed if subspace membership is known.
- **Gradient of Rᶜ (Eq. 9)**: Direction to move tokens to reduce (compress) the coding rate; involves an expensive matrix inverse.
- **Softmax / auto-correlation**: Token-pair similarity after subspace projection, turned into a probability distribution — this is the attention map.
- **SSA vs. MSSA**: Subspace Self-Attention (single head: project, compute similarity, weighted sum) vs. Multi-Head Subspace Self-Attention (K heads stacked/aggregated) — structurally identical to standard multi-head attention.
- **ISTA (Iterative Shrinkage-Thresholding Algorithm)**: Classic sparse-coding optimizer alternating a gradient step and a soft-thresholding (ReLU-like) step.

### Page 7

- **Dictionary D**: A learnable, orthogonal basis/codebook used to make token representations sparse.
- **LASSO**: Optimization balancing sparsity (ℓ₁ penalty) against reconstruction accuracy.
- **ISTA**: (see above) — used here to solve the LASSO sparsification problem via an unrolled step.
- **ReLU**: max(0, x); emerges naturally from soft-thresholding + non-negativity, matching standard Transformer MLP activations.
- **Proximal Gradient Descent**: Optimization method for objectives with a smooth part and a non-smooth (ℓ₁) part; the "proximal" step is the soft-thresholding/ReLU operation.

### Page 8

- **Layer-Dependent Parameters**: Each CRATE layer learns its own subspaces (U[K]ₗ) and dictionary (Dₗ), unlike ReduNet's globally fixed parameters — this is what enables scaling to large datasets.
- **ReduNet**: (see above) — contrasted again as using one global model vs. CRATE's per-layer learned models.
- **CLS Token**: A special token prepended to the sequence (ViT convention) whose output representation is used for classification.
- **Lion Optimizer**: A memory-efficient, sign-momentum-based optimizer used for training CRATE instead of AdamW.

---

## Part 2: All Flagged Reader Questions, by Page

### Page 1

- Why specifically a "mixture of Gaussian distributions"?
- How exactly does a Self-Attention layer "compress" and an MLP "sparsify," in mathematical terms?
- If this is a white-box model, why does it still need to be trained via backpropagation?

### Page 2

- What exactly is the "sparse rate reduction" objective (promised as Equation 1)?
- How can the paper claim to unify Transformers and Diffusion when they're used for different tasks (classification vs. generation)?
- If white-box, why still train via backprop? (subspace bases/dictionaries still need to be learned from data)

### Page 3

- What exactly are equations (7) and (8) — the formal coding-rate definitions?
- How is the arbitrary correlation structure among tokens handled, given the paper only requires each token's marginal distribution to be a Gaussian mixture?
- If rate reduction is rotation-invariant, how do you choose the specific sparsity-promoting coordinates? (hinted: a global dictionary D)

### Page 4

- Why is directly optimizing Equation (1) so computationally hard?
- If each layer learns its own U[K]ₗ, do the "subspaces" change meaning as data passes deeper into the network?
- What happens mathematically when N > 1 (more than one token)?
- What exactly is σₗ (the noise level) in Equation (3)?

### Page 5

- What exactly is Rᶜ (the conditional coding rate) — definition was cut off, expected at top of next page?
- How do we get from the N=1 case to the full Query-Key-Value mechanism when N > 1?
- Why is there a factor of 1/(2σ²) in the softmax of Equation (6)?

### Page 6

- If the MLP is an ISTA step, does that mean any MLP could be replaced by this specific formula?
- Why is the expansion term R(Z) maximized instead of minimized (to avoid representation collapse)?
- What is the role of the dictionary D mentioned in the abstract for the MLP step?
- How exactly are the bases U[K] learned (forward pass uses them, backward pass updates them)?

### Page 7

- How is the dictionary D actually learned?
- Why is the non-negativity constraint added in Equation (16)?
- Is D truly orthogonal in practice, or only approximately (via regularization)?
- What is the role of the skip connection in the derived MLP (Equation 17)?
- What does the full CRATE architecture look like end-to-end (referencing Figure 2)?

### Page 8

- What exactly are the "minor modifications" to the architecture (pointed to Appendix B.1)?
- Why use the Lion optimizer instead of the more common AdamW?
- How do the layer-wise subspaces U[K]ₗ and dictionaries Dₗ actually get updated during backprop?
- Why use a CLS token instead of global average pooling?
- Does the paper address generative tasks, given its ties to diffusion models?