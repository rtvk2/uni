# White-Box Transformers via Sparse Rate Reduction (CRATE)

a transformer is not empirical black boxes; it's an unrolled optimizer for a single objective (sparse rate reduction). self-attn = compression step, mlp = sparsification step.

coding rate: how many bits are required to encode or compress a set of data vectors/tokens
reduction: lowering that coding rate by compressing the volume and redundancy of data tokens into compact, low-dimensional subspaces
transformer: architecture

white-box vs black-box:
black-box (std ViT) → works, no idea why each layer does what it does
white-box (CRATE) → every layer = one step of a known optimization, provably

three paradigms it tries to unify:

1. transformers → good perf, no math interpretation
2. diffusion → good generation, no interpretable/compact representation
3. structure-seeking (sparse coding, rate reduction, contrastive) → interpretable (can unroll → white-box) but doesn't scale to real data

keep MCR² math* + add per-layer learnability → scales and closes the gap.

---

## 1. objective: sparse rate reduction

target representation: mixture of $K$ low-dim gaussians, zero mean, orthonormal bases $U_k$, sitting in disjoint incoherent subspaces.

we want:
- rate reduction $ΔR = R − R^c$ large → subspaces well separated, each individually cheap to code;  (what do the subspaces represent?)
- incoherence → bases $U_k$ mutually ~orthogonal (else two classes overlap, bad for discrimination)
- sparsity → rate reduction alone is rotation-invariant (rotate the whole blob, ΔR unchanged) so add explicit $\ell_0$ penalty to pin down coordinates ; Can we guess why 

unified objective (eq 1):
$$\max_f \; \Delta R(Z) - \lambda \|Z\|_0, \quad Z = f(X)$$

$R, R^c$ defined via log-det lossy coding rate (bits to encode up to precision ε):

$$R(Z) = \frac12 \log\det\left(I + \frac{d}{N\epsilon^2} ZZ^*\right)$$

$R^c$ = sum of per-subspace coding rates (eq 8) — cheap if you already know cluster membership.

directly optimizing eq 1 → intractable (log-det terms non-convex, $\ell_0$ combinatorial). so: unroll.

why is the mean of all gaussians 0
how do we get Uk; steps in backprop
D^l vs D (check block diag); what is global

---

## 2. unrolled incremental optimization

break global map $f$ into $L$ small local steps:

$$Z^{\ell+1} = f^\ell(Z^\ell), \quad \ell = 0, \dots, L-1$$

vs ReduNet (prior white-box net, also from rate reduction):
- ReduNet → one global fixed math model for all layers
- CRATE → local model per layer, params $U_{[K]}^\ell, D^\ell$ learned via backprop

→ forward pass = optimization (using current local model), backward pass = learning (updates the local model). this split is the actual novelty over ReduNet, is why CRATE scales and ReduNet doesn't.

each layer = two-step alternating min:
1. compress: min $R^c$ → attention (MSSA)
2. sparsify: min $\lambda\|Z\|_0 - R(Z)$ → mlp (ISTA)

---

## 3. attention from denoising (toy case, N=1)

idealized: single token, corrupted by gaussian noise, mixture-of-gaussians prior.

tweedie's formula (eq 5): optimal denoised estimate = noisy input + gradient of log-density (score fn)
$$\hat z = z^\ell + (\sigma^\ell)^2 \nabla_z \log q^\ell(z)$$

plug in gaussian mixture density → score fn works out to a softmax over subspace projections (eq 6). structurally = attention, but N=1 so it's not full self-attn yet, just the building block.

note: temperature in the softmax is $1/(2\sigma^2)$ → high noise → flat softmax (more averaging), low noise → sharp softmax (picks one subspace). same role as temperature scaling in normal transformers.

link to diffusion: this whole denoising-toward-cleaner-representation view is basically the reverse diffusion process (score-based), not just an analogy.

---

## 4. attention from compression (N>1, real case)

tokens aren't iid → correlations matter (co-occurrence etc) → compress the *set* together, not token by token.

R^c formally (eq 8) = sum over k of per-subspace coding rate, using projections $U_k^* Z$.

gradient descent step on R^c (eq 9) → approximates to:

$$Z^{\ell+1/2} = Z^\ell + \kappa \cdot \text{MSSA}(Z^\ell)$$

single head (SSA, eq 11): project into subspace $k$ via $U_k^*$, compute token-token similarity (auto-correlation $(U_k^*Z)^*(U_k^*Z)$), softmax over it → attention map → weighted sum of projected tokens.

multi-head (MSSA, eq 12): stack $K$ heads, aggregate.

key point: in this derivation, Q = K = V = $U_k^*$ — literally the same projection, not three separate learned matrices like std transformer. paper explicitly cites hinton suggesting Q=K=V independently — treated as external validation.

special case N=1 → MSSA reduces back to the toy denoiser from section 3. consistent.

so: attention = residual step of gradient descent on the compression term. two derivations (denoising, compression) agree at N=1.

---

## 5. mlp from sparsification (ISTA)

remaining term: $\max R(Z) - \lambda\|Z\|_0$ — directly computing $\nabla_Z R(Z)$ needs a matrix inverse → poorly scalable.

workaround: introduce learnable orthogonal dictionary $D$ ($D^*D \approx I$). since orthogonal, $R(Z) = R(DZ)$ → can push the sparsity constraint onto $DZ$ instead without changing R.

reframe as LASSO (same structure as the earlier unrolling notes on LASSO/ISTA):

$$\min_Z \lambda\|Z\|_1 + \|Z^{\ell+1/2} - DZ\|_F^2, \quad Z \ge 0$$

unroll one proximal gradient / ISTA step → relu update (eq 17):

$$Z^{\ell+1} = \text{ReLU}\left(Z^{\ell+1/2} + \eta D^*(Z^{\ell+1/2} - DZ^{\ell+1/2}) - \eta\lambda D^*D\mathbf{1}\right)$$

→ linear projection + residual + bias subtraction + relu = exactly a transformer mlp block. relu falls out of soft-thresholding + non-negativity constraint, same mechanism as the earlier ISTA.

non-negativity constraint added following as it improves sparse coding perf empirically, not derived from first principles.

---

## 6. full CRATE layer

combine both steps (eq 18):

$$Z^\ell \to^{\text{MSSA}} Z^{\ell+1/2} \to^{\text{ISTA/mlp}} Z^{\ell+1}$$

stack $L$ of these → full net. name: CRATE = coding rate transformer.

params $U_{[K]}^\ell$, $D^\ell$ differ per layer (unlike ReduNet) — layer 1 subspaces describe raw input distribution, layer 10 subspaces describe the deep/abstract representation. mirrors how features get more abstract with depth in normal deep nets, but here it's explicit in the math, not just observed.

---

## 7. experiments 

two goals stated:
1. verify layers actually do what math says (compress/sparsify; checked internally, not just accuracy)
2. show competitive perf despite using the simplest version of every derived component

setup:
- sizes: Tiny/Small/Base/Large (mirrors ViT naming → fair param-matched comparison)
- CLS token for classification (std ViT convention, not required by the theory itself)
- Lion optimizer
- pretrain ImageNet-1K, transfer to CIFAR10/100, Flowers, Pets
- "minor modifications" deferred to append likely a learnable weight matrix replacing the fixed $\frac{p}{N\epsilon^2}$ scaling in MSSA, for stability


## Page 1

### Key Terms

- **White-box vs black-box**: A black-box model (standard ViT) works well but its internal reasoning is unclear. A white-box model is mathematically transparent, with each layer performing a specific, provable function.
- **Sparse rate reduction**: The unified objective combining rate reduction (compression into low-dimensional subspaces) and sparsity (representations with mostly zero entries).
- **Rate reduction (compression)**: Representing data efficiently using as few bits as possible by fitting it into low-dimensional subspaces.
- **Sparsity**: Ensuring the representation uses mostly zeros, making it compact and easy to handle.
- **Mixture of low-dimensional Gaussian distributions**: The assumption that the ideal representation forms distinct clusters lying on flat, lower-dimensional planes rather than filling the whole space.
- **Tokens**: Small patches or units data is chopped into before being processed by the network.

### Questions and Answers

- **Why specifically a mixture of Gaussian distributions?** This is justified later as the optimal geometric structure for efficient coding.
- **How exactly does self-attention compress and the MLP sparsify, in mathematical terms?** This is the core derivation, covered in Sections 2.3 and 2.4.
- **If this is a white-box model, why does it still need training via backpropagation?** The architecture is white-box, but the subspace bases and dictionaries still need to be learned from data.

---

## Page 2

### Key Terms

- **Score function (diffusion models)**: The mathematical guide indicating which direction to move to remove noise from a corrupted image, usually approximated by a generic (black-box) network.
- **Unrolling optimization**: Turning each iteration of an iterative algorithm into a single layer of a neural network, so the network's operations are mathematically transparent.
- **Pretext task (contrastive learning)**: An auxiliary task used to force a network to learn good representations without manual labels.
- **Parsimonious representation**: A representation that is compact, simple, and uses the fewest possible components to describe the data.

### Flagged Questions and Answers

- **What exactly is the sparse rate reduction objective mentioned at the end of this page?** It is formally specified later in Equation (1), on page 4.
- **How can Transformers and diffusion models be unified when they're used for different tasks (classification vs generation)?** The authors treat both as incremental mappings that push data toward a structured distribution, clarified in the technical sections.
- **If the architecture is white-box and derived from math, why train it with backpropagation?** The math determines the layer structure, but the specific subspace bases and dictionaries still need to be learned from data.

---

## Page 3

### Key Terms

- **Piecewise linearized representation**: Flattening complex, curved data into a set of flat linear subspaces glued together, making it easier to process mathematically.
- **Incoherent subspaces**: Subspaces that are close to orthogonal (perpendicular) to each other, so clusters are clearly separated rather than overlapping.
- **Rotation invariance vs sparsity**: Rate reduction stays the same if you rotate a compressed cluster of data, but practical storage requires alignment with fixed axes, so a sparsity penalty is added to fix the coordinate system.
- **Lossy coding rates (R and R^c)**: Formulas that count how many bits are needed to store data with some acceptable loss. R is the rate for the whole set; R^c is the rate conditional on knowing which subspace the data belongs to.

### Flagged Questions and Answers

- **What exactly are equations (7) and (8)?** They are the formal log-determinant definitions of the coding rates, introduced on page 4/5.
- **How do you handle arbitrary correlation among tokens?** The authors simplify by only requiring the marginal distribution of each individual token to be a mixture of Gaussians, rather than modeling the full joint distribution.
- **If rate reduction is rotation invariant, how do you fix the coordinates for sparsity?** The paper uses a global dictionary D, introduced in Section 2.4, to fix the coordinate system.

---

## Page 4

### Key Terms

- **ℓ0 norm**: Counts the number of non-zero entries in a matrix; promoting this forces the representation toward sparsity.
- **Unrolled optimization**: Converting an iterative algorithm into a neural network where each layer performs one step of that algorithm.
- **ReduNet**: A prior white-box network built on the rate reduction principle, using globally fixed subspaces rather than layer-specific learned ones.
- **Conditional expectation**: The optimal predictor of a clean signal given a noisy observation, minimizing average squared error; used as the gold standard for denoising.

### Flagged Questions and Answers

- **Why is optimizing Equation (1) directly so hard?** The difficulty comes from the non-convexity of the log-determinant terms and the combinatorial nature of the ℓ0 norm.
- **If each layer learns its own subspaces, do they change meaning through the network?** Yes. Layer 1's subspaces describe the input, deeper layers' subspaces describe increasingly abstract representations, similar to how features grow more abstract in deep networks generally.
- **What happens when there is more than one token (N > 1)?** Section 2.2 only covers the single-token case to build intuition; the full multi-token, multi-head derivation comes in Section 2.3.
- **What is the noise parameter in Equation (3)?** It is the noise level at that layer. As depth increases, the noise shrinks and the representation becomes cleaner, mirroring a reverse diffusion process.

---

## Page 5

### Key Terms

- **Tweedie's formula**: A statistical result stating the best estimate of a clean signal is the noisy observation plus the gradient of the log-density of that observation.
- **Score function**: The gradient of the log-likelihood, pointing toward the direction where probability of a clean signal increases fastest.
- **Kronecker product**: A matrix operation used to combine softmax weights with an identity matrix, routing a token to the correct subspace.
- **Lossy coding rate R(Z)**: A formula measuring how many bits are needed to store a matrix Z within an acceptable error (quantization precision); reflects the spread of data in high-dimensional space.
- **Quantization precision**: The acceptable error allowed when compressing data; larger values allow more aggressive compression at the cost of detail.

### Flagged Questions and Answers

- **What exactly is R^c (the conditional coding rate)?** The definition is cut off at the end of this page; it is the coding rate conditional on knowing a token's subspace, defined formally at the start of page 6.
- **How do we get from the single-token case (Equation 6) to full query-key-value attention for N > 1 tokens?** This is what the rest of Section 2.3 does: the auto-correlation among tokens acts as the key-query similarity matrix, producing full softmax attention over tokens.
- **Why is there a temperature-like factor in the softmax of Equation (6)?** It is inversely proportional to the noise level. High noise flattens the softmax (more averaging); low noise sharpens it (picks the best subspace), similar to temperature scaling in practical Transformers.

---

## Page 6

### Key Terms

- **Conditional coding rate R^c (Equation 8)**: The number of bits needed to encode tokens if their subspace/class is already known, summed across subspaces.
- **Gradient of R^c (Equation 9)**: Indicates the direction to move tokens to reduce the conditional coding rate (compress them); involves an expensive matrix inverse that is later approximated.
- **Auto-correlation / softmax attention map**: Dot products between token projections in a subspace, turned into a probability distribution via softmax, indicating token similarity.
- **SSA vs MSSA**: SSA (Subspace Self-Attention) is a single attention head; MSSA (Multi-Head Subspace Self-Attention) stacks multiple heads and aggregates them, matching standard multi-head attention.
- **ISTA (Iterative Shrinkage-Thresholding Algorithm)**: A classic algorithm for sparse coding problems that alternates a gradient step with a soft-thresholding (ReLU-like) step.

### Flagged Questions and Answers

- **If the MLP is an ISTA step, can any MLP be replaced by this formula?** Yes, Section 2.4 shows the MLP block can be replaced by a ReLU-based update derived from proximal gradient descent, making it white-box.
- **Why is the expansion term R(Z) maximized instead of minimized in Equation (13)?** Minimizing sparsity alone would collapse the representation to zero. R(Z) measures the volume of the data; maximizing it keeps the representation diverse and prevents collapse.
- **What is the dictionary D mentioned for the MLP?** It is introduced formally on page 7 as part of the ISTA/sparsification step.
- **How are the subspace bases learned?** They are parameters trained via backpropagation: the forward pass uses them to compress, and the backward pass updates them to fit the data distribution.

---

## Page 7

### Key Terms

- **Dictionary D**: A learnable, orthogonal set of basis vectors used to transform compressed tokens into a space where they become sparse.
- **LASSO**: A method balancing sparsity (via an ℓ1 penalty) and reconstruction accuracy, used to formulate the sparsification problem.
- **ISTA**: The optimization algorithm used to solve LASSO, alternating a gradient step with soft-thresholding.
- **ReLU**: The activation function max(0, x), which emerges naturally from the soft-thresholding step combined with a non-negativity constraint.
- **Proximal gradient descent**: An optimization method for functions with both a smooth part and a non-smooth part (like the ℓ1 norm), where the proximal step applies soft-thresholding.

### Flagged Questions and Answers

- **How is the dictionary D learned?** Like the subspace bases, it is learned via backpropagation, unlike classical sparse coding where dictionaries are often fixed or learned offline.
- **Why is a non-negativity constraint added in Equation (16)?** The authors cite prior work suggesting non-negative sparse coding improves performance; this constraint leads naturally to the ReLU activation.
- **Is D truly orthogonal?** The authors assume this approximately; in practice it may be enforced through regularization or hold approximately after training.
- **What about the skip connection in the MLP?** Equation (17) includes a residual term mirroring the skip connection in standard Transformer MLPs, and it emerges directly from the optimization algorithm rather than being added by hand.
- **What does the full architecture look like?** Figure 2 shows a single CRATE layer; the full network stacks these layers, starting with a tokenizer for raw patches and ending with a classification head.

---

## Page 8

### Key Terms

- **Layer-dependent parameters**: Subspace bases and dictionaries that differ at each layer, allowing each layer to model the distribution of its own input rather than sharing one global model.
- **ReduNet (contrast)**: Used a single global mathematical model across all layers, limiting its flexibility compared to CRATE's per-layer learned parameters.
- **CLS token**: A special token prepended to the input sequence in vision transformers; its output after processing serves as the aggregate representation used for classification.
- **Lion optimizer**: An optimization algorithm (Evolved Sign Momentum) used for training, chosen for memory efficiency and effectiveness at scale.

### Flagged Questions and Answers

- **What are the "minor modifications" to the architecture mentioned here?** Detailed in Appendix B.1; likely includes replacing a fixed scaling factor in MSSA with a trainable weight matrix for added flexibility.
- **Why use the Lion optimizer instead of AdamW?** Not explained on this page beyond a pointer to Appendix B.1; Lion is generally known for lower memory use and faster convergence in some settings.
- **How do the layer-wise subspaces and dictionaries actually get updated during training?** Through standard backpropagation and automatic differentiation, since they are just parameter matrices receiving gradients from the loss.
- **Why use a CLS token instead of global average pooling?** This follows standard ViT practice for a fair comparison; the rate reduction theory itself does not require a CLS token specifically.
- **What about generative tasks?** Not addressed in the experiments, which focus on classification and transfer learning, though the paper's theoretical connection to diffusion models suggests possible future extension to generation.