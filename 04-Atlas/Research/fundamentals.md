covering paper sections 1-4

### CVPR 2022:

ml → induction (from problems & answers); like flashcards  
opt → prescription (from problems & evaluations); like trying different routes to work0 every day and tracking your commute time (the evaluation) until you discover the single fastest path.

classic opt → hand-built on theory and experience; practitioners only pick the algo  
l2o → experts propose templates & training procedures; practitioners pick template, prepare training data and procedures, thereby creating a trained algo for future use

opt algos are similar to nn layers:

$x^{k+1} = nonlin(lin(x^{k})+offset)$; opt algo  
$x^{k+1} = ReLU(weights(x^{k})+bias)$; single layer in nn

only difference is that in nn (except rnn) → nonlin, lin functions and offset (parameters) change significantly with k (iterations);  
so opt algo similar to rnn

take classic algo, unroll iterations to a finite nn → untie parameters, allowing them to learn optimal distinct weights for each layer (as opposed to them being mathematically forced to be identical at every step);

slow math solver → fast specialised (learns from trainign data) solver  
nn→ learn to optimise → opt

opt → relax/neuralise → nn

two nn architectures for opt:

1. model-free (black-box)
    
    like traditional dl→ take inputs, train, give outputs; ignores mathematical structure of opt.  
    pros: extremely fast inference (very few layers)  
    cons: massive number of parameters, very slow training, poor generalisation
    
2. model-based (white/gray box)
    
    start with optimization algo & neuralise  
    apply dl to specific parts of algo that need acceleration or flexibility.  
    types:
    
    algorithm unrolling (au): classic iterative algo steps → nn layers  
    plug-n-play (pnp): classic algo → replace one specific math step (often prior or regularization) with pre-trained nn (like image denoiser)  
    deep equilibrium models (deq): design network to find fixed point (eq state) → convergence
    

algorithm unrolling (au):

two key steps:

1. pick a classic iteration and unroll it to nn
2. select a set of nn parameter to learn

LASSO example:

$x^{\text{lasso}} \leftarrow \text{minimize}_x \frac{1}{2}\|Ax-b\|_2^2 + \lambda\|x\|_1$

trying to recover xtrue that passed through a physical system A and corrupted by noise. b is observed measurement.  
need to minimise two competing forces:

1. data fidelity: $\frac{1}{2}\|Ax-b\|_2^2$ → ensures guess signal matches physical measurements
2. regularizer: $\lambda\|x\|_1$ penalty that forces solution to be sparse

1→ tries to fit messy noise → makes lots of values non zero

2→ tries to min values to 0 to clean signal → predicted measurements stop matching raw data

l1 vs l2 norm:  
for an optimisation problem, you look at the derivative and walk down the slope till it becomes exactly flat (derivative = 0)

l1 → like $y= |x|$  
l2 → like $y= x^2$

for l2 norm, we get a smooth curve (parabola), so we can move down and get to x=0. but for l1 norm, it is a sharp v-shaped curve → anywhere on left, slope =-1; on right, slope = 1. however it is non differentiable at x=0

this is an issue since grad descent requires derivative to know which direction to step, whereas the point of LASSO is to force sparsity. if algo reaches 0 → derivative can’t be computed.

Iterative Soft-Thresholding Algorithm (ISTA):

goes about this issue by separating tasks:

1. uses standard smooth derivatives to handle least squares part of eq.
2. uses separate non-derivative mathematical rule (soft-thresholding) to forcibly handle the sharp undefined corners of l1.

$x^{\text{k+1}} = η_{λα} (x^{\text{k}} − αA^\text{T} (Ax^{\text{k}} − b))$

here grad descent → $x^k - \alpha A^T(Ax^k - b)$; minimizes LSE (least-squares error). $α$ → step size (req for convergence)

& soft-thresholding → $\eta_{\lambda\alpha}(\cdot)$; function applied element-wise to the result of the gradient step which mathematically shrinks small values toward zero (proxy for sparsity constraint)

note:

$(Ax^k - b)$ → error: predicted minus measured.

$A^T$ projects that error back into signal space.

subtract a small fraction $α$ of that error to update guess.

$\lambda\alpha$ → acts as the noise gate; $η$ → filter

value in signal < gate → goes to 0  
value in signal > gate → shrunk slightly, but remains

Gregor & LeCun (2010), proposed freeing parameters of ISTA.

wkt $x^{\text{k+1}} = η_{λα} (x^{\text{k}} − αA^\text{T} (Ax^{\text{k}} − b))$  
→ $x^{k+1} = η_{λα} (x^k - \alpha A^T A x^k + \alpha A^T b)$

→ $x^{k+1} = η_{λα} ((I - \alpha A^T A)x^k + \alpha A^T b)$

let $W_1 = \alpha A^T$, $W_2 = I - \alpha A^T A$, $\theta = \lambda\alpha$ (the threshold)

→ $x^{k+1} = \eta_\theta(W_1 b + W_2 x^k)$

![image.png](attachment:f4776899-2ea1-463e-bc0a-fdc704c6c3bf:image.png)

structurally identical to standard recurrent neural network (RNN) or residual network layer but with untied parameters.

**$W_2 x^k$**: linear weight matrix multiplied by the hidden state from previous layer.  
**$W_1 b$**: linear projection of your input data $b$ (a bias being added at every step)

**$\eta_\theta$**: non-linear activation function (soft-thresholding, comparable to ReLU)

is it learning for a fixed sparsity or can it vary in xtrue generated

how does it work iwth x0 being arbitrary

since the parameters are free and learnable for every layer, its inference is much faster (almost like taking shortcuts).  
note that A is fixed. →> theory backing it  
→ LISTA

*but how does it end up being much faster, it still needs to learn weights?

it isnt faster at learning, but at inference

ISTA → requires small steps to assure math convergence since it is a gen-purpose algo. it is limited by strict, generic mathematical bounds. (step size ($α$) must be strictly bounded by the maximum eigenvalue, the inverse of ****Lipschitz constant, of the matrix $A^TA$. If it steps any larger, the math says it might overshoot the minimum and explode) → $α<2/L$ , where $L=λ_{max}(A^TA)$

LISTA → training takes time but inference is instantaneous. since it’s based on a specific dataset, it can take shortcuts from the statistics it learned from data. it takes these shortcuts in two ways:

1. preconditioning
    
    1. geometry of data fidelity term function → dictated entirely by its Hessian matrix
        
    2. condition number ($κ$) of Hessian matrix ($A^TA$) → $κ(A^TA)=λ_{max}(A^TA)/λ_{min}(A^TA)$
        
        $κ=1$: all eigenvalues are equal → level sets (contour lines) of the objective function are perfect hyperspheres (bowl shaped); negative gradient points directly at the global minimum.  
        $κ≫1$: matrix is ill-conditioned → level sets (contour lines) of objective function are stretched, skewed ellipses (canyon shaped); gradient points orthogonal to the contour lines → has to zig-zag to reach convergence.
        
    3. ISTA→ forced into being dependent on condition number
        
    4. LISTA → unties the weights to learn $W_2$, acting as a learned preconditioner
        
        learns $W_2$ that actively modifies the eigenspectrum of the iteration matrix, artificially clustering the eigenvalues of the effective Hessian closer to 1 → allows level sets to be closer to hyperspheres as extreme eigenvalues are suppressed. **
        
2. exploiting the prior
    
    1. ISTA → relies on a generic l1 prior, forcing it to start completely blind at $x^0=0$ for every new data point.
        
    2. LISTA → replaces this generic assumption with the probability distribution of the dataset. because it retains a memory of previous training pairs, the first unrolled layer acts as a data-driven projection matrix. when computing $x_1=η_θ(W_1b)$, $W_1$ instantly correlates the new measurement b against learned patterns of how the system's physics corrupts the signal. this projects the initial guess directly onto the true data manifold (closer), bypassing the need to search from zero → drastically reduces the steps required to converge.
        
        ![image.png](attachment:d539d804-a13d-4dc5-aba0-f48f311570c5:image.png)
        

[https://web.stanford.edu/~boyd/cvxbook/bv_cvxslides.pdf](https://web.stanford.edu/~boyd/cvxbook/bv_cvxslides.pdf) pg299  
training LISTA involves 3 steps (almost identical to a traditional nn):

1. generating dataset
    
    1. fix a random measurement matrix $A$ (represents your specific hardware/physics).
    2. artificially generate thousands of sparse ground truth signals $x^{\text{true}}$ with varying supports (randomly placed non-zero spikes), along with observed measurements:
    3. multiply by $A$ and add simulated noise to get obs measurements $b$ & form supervised training pairs: $(x^{\text{true}}, b)$
2. forward pass and loss
    
    1. fix a intentionally small $K$ (e.g., $K=16$ layers/iterations)
    2. feed batch of $b$ through unrolled layers to get final prediction at last layer $x^K(b)$
    3. loss function is standard l2 norm (MSE) comparing with $x^*$ (ground truth)
3. back-propagation
    
    1. apply Stochastic Gradient Descent (SGD) to minimize the l2 loss: $\sum \|x^K(b) - x^{\text{true}}\|_2^2$
    2. update free parameters ($\theta^k, W_1^k, W_2^k$) for every layer so that loss is min.
    
    *soft-thresholidng doesn’t rely on being differentiable; how does it work?
    
    even in ReLU, there is a sharp corner at 0.  
    the answer is that nn handle these sharp corners using a concept from convex optimization called subgradients (or subderivatives).
    
    soft-thresholding function → $η_θ(x)=sgn(x)max(∣x∣−θ,0)$
    
    1. If $∣x∣>θ$ → gradient = 1
    2. If $∣x∣<θ$ → gradient = 0
    3. If $∣x∣$ is exactly $θ$ → derivative doesn't exist. however, frameworks like PyTorch or TensorFlow assign a valid subgradient** (usually 0 or 1) at that exact infinitesimal point. this is possible as the probability of a continuous floating-point value landing exactly on $θ$ is effectively zero, so it doesn't disrupt gradient flow.

result: the learned network hits a lower error ~50x faster than classic ISTA. since it is trained on $x^{\text{true}}$, it actually converges to a better physical approximation of the true signal than just mathematically solving the LASSO objective.

![image.png](attachment:4ede7bd8-f299-4b13-9812-6a1dd70f731b:image.png)

key differences against traditional nn can be seen in this comparison table:

![image.png](attachment:e28be248-0f96-4cc3-b3a3-2cae64dff615:image.png)

applications:

1. applied to other classic operator-splitting algos (ADMM, PDHG, etc.)
    
2. CT reconstruction: $b = Ax + \text{noise}$ (where $A$ is the Radon transform). au yields much cleaner images than classic total variation (tv) or pure black-box cnn.
    
    ![image.png](attachment:384bb721-ae32-44e1-a717-bfb9396ca910:image.png)
    
3. image de-blurring: $b = k * x + \text{noise}$ (where $k$ is an unknown blurring kernel). au correctly recovers the blurring kernel and creates sharper images with less distortion.
    
    ![image.png](attachment:21d301b3-3911-4155-8b5c-478f6873b42b:image.png)
    

challenges of au:

1. too many parameters: learning distinct $W_1^k$ and $W_2^k$ for every layer means $\mathcal{O}(n^2K + mnK)$ parameters (as wkt $A ∈ R^{m×n}$ → W1 (∼$A^T$) takes $mn$ parameters, and W2 (∼$A^TA$) takes $n^2$ parameters per layer) → very slow training. it forces a trade-off when choosing $K$ (too small = poor convergence, too large = exploding/vanishing gradients) since it dictates increase in parameters.
2. interpretability: critical fields (medical imaging) require white-box explainability. (au isn’t pure white-box since parameters are still black-box)
3. generalizability: if given out-of-distribution (unseen) data, the solver should not fail terribly. worst-case performance must atleast match classic math algo.  
    otherwise it sacrifices math reliability.

advances in LISTA:

1. weight coupling (CP) → LISTA-CP
    
    1. theoretical analysis of LISTA (without noise) showed that as iterations $k \to \infty$, then $W_2^k + W_1^k A \to I$ and the threshold $\theta^k \to 0$. instead of letting the network slowly learn this, researchers enforced it mathematically from layer 1: $W_2^k = I - W_1^k A$.
    2. this eliminates the need to learn $W_2$ as only $W_1^k$ needs be learnt (fronm which $W_2$ can be calculated).
    3. → new iteration: $x^{k+1} = \eta_{\theta^k} (x^k + W_1^k(b - Ax^k))$
    
    result**:** drastically reduces free parameters from $\mathcal{O}(n^2K + mnK)$ to just $\mathcal{O}(mnK)$, making training highly stable and faster.
    
2. support selection (SS) → LISTA-SS
    
    1. at each iteration $k$, find the largest few components (the most obvious true signal spikes) and let them completely bypass the soft-thresholding step.
    2. soft-thresholding is great at killing noise, but it artificially shrinks true peaks (inducing bias). bypassing the obvious spikes → eliminates shrinkage penalty.
    3. the exact number of components to bypass is governed by a trainable fraction parameter.
    
    result: accelerates the convergence rate and achieves a significantly lower final reconstruction error (NMSE) by eliminating attenuation (shrinkage bias) on the true non-zero components of the signal.
    
3. combination of the two → LISTA-CPSS
    
    result**:** drastically improves both convergence speed and final normalized mean square error (NMSE) over standard LISTA.
    
4. tied W across all iterations (Ti) → TiLISTA
    
5. mutual coherence → ALISTA (analytic LISTA)
    
6. through encoder-decoder pair → Robust LISTA
    
7. using symmetric positive semidefinite U instead of learning W → ada-LISTA
    
8. using hybrid-thresholding, analytic formulas for parameters, 3 hyperparameters for grid search → HyperLISTA
    

error vs iterations:

## Gregor and Yann LeCun:  

2010:
In Sparse Coding (SC), input vectors are reconstructed using a sparse linear combination of basis vectors. SC has become a popular method for extracting features from data. For a given input, SC minimizes a quadratic reconstruction error with an L1 penalty term on the code. The process is often too slow for applications such as real-time pattern recognition. We proposed two versions of a very fast algorithm that produces approximate estimates of the sparse code that can be used to compute good visual features, or to initialize exact iterative algorithms. The main idea is to train a non-linear, feed-forward predictor with a specific architecture and a fixed depth to produce the best possible approximation of the sparse code. A version of the method, which can be seen as a trainable version of Li and Osher’s coordinate descent method, is shown to produce approximate solutions with 10 times less computation than Li and Osher’s for the same approximation error. Unlike previous proposals for sparse code predictors, the system allows a kind of approximate “explaining away” to take place during inference. The resulting predictor is differentiable and can be included into globally-trained recognition systems.

sparse coding ↔ LASSO (sparse signal recovery)

dictionary matrix $W_d$ ↔ measurement matrix $A$  
input vector $X$ ↔ observed measurement $b$  
sparse code vector $Z$ ↔ true signal $x^{true}$  
filter matrix $W_e$↔ feed forward projection matrix $W_1$  
mutual inhibition matrix $S$ ↔ recurrent/hidden state matrix $W_2$  
shrinkage function $h_\theta$ ↔ soft-thresholding operator $n_\theta$

## Yonina Eldar:

Deep Learning for Biomedical Image Reconstruction (2023) & other books: [https://www.weizmann.ac.il/math/yonina/publications/books](https://www.weizmann.ac.il/math/yonina/publications/books)

**Model-Based Deep Learning for Sensing and Imaging: Efficient and Interpretable AI**

[https://arxiv.org/pdf/2306.04469](https://arxiv.org/pdf/2306.04469)  
[https://www.youtube.com/watch?v=Z4AIMQCNbq8](https://www.youtube.com/watch?v=Z4AIMQCNbq8)

[https://www.youtube.com/watch?v=0iNuHfUAcbU](https://www.youtube.com/watch?v=0iNuHfUAcbU)

Q&A Transcript: ([https://www.youtube.com/watch?v=Z4AIMQCNbq8](https://www.youtube.com/watch?v=Z4AIMQCNbq8))

[[17:56](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=1076)] - unfolding vs. hybrid approach; covered

[[23:08](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=1388)] - learning parameters vs. fixed parameters (wrt to $\lambda$ and free params); covered

[[23:48](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=1428)] - source of training data ($x^{true}$, $b$); covered

qs: is the training data produced with this function to begin with?  
ans: where does the training data come from? that's the nice thing about this approach, is that we use the model in several ways:

1. we use the model to generate the architecture itself.
    
2. where I use the model is adding the [model equation] to the metric for learning.
    
3. use it to generate training data. So I don't even have to have external training data.
    

[[25:30](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=1530)] - comparison to Physics-Informed Neural Networks (PINNs)

PINNs are based on physics too. so why not just use them? PINNs only use physics for the loss function. the power of unrolling comes from the fact that it uses physics to build the actual architecture of the model.

[[27:01](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=1621)] - learning biases & estimation theory

special cases where we can make the equivalence, like write down the Gaussian model, we show that it has that interpretation, but this [unfolding] is much more generic.

[[28:17](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=1697)] optimization loss vs. machine learning metric

qs: so the metric function that you talk about, that's like the loss function that machine learning talks about?  
ans: in principle we have two different losses.

1. the loss function of the optimization... my architecture is going to come from this
2. You have different loss functions. I can make them the same, but I could also make them different. If I use [the optimization metric] as my loss, then I don't need supervision... If I do have training data, then I would use this plus [matching the training data].
3. once I have the architecture... I have an external learning function to learn these parameters.

[[33:49](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=2029)] - parameter convergence across layers

qs: when you are learning these different lambdas, can you look at the different lambdas to see if there are patterns?  
ans: something that we started looking at. I would like to think that as the layers move forward, they are converging to this when A is known... but if it converged to that, it couldn't solve the problem in 10 iterations, because it needs 20,000 iterations to converge to that. so in some sense, if it's solving the problem in 10 iterations, it's probably converging to something totally different → taking a totally different path; not just a faster ISTA

[[36:31](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=2191)] cheating optimization bounds

qs: when you're doing this learning, and you have training data, aren't you saying that you're sampling the gradient in multiple places?  
ans: yeah, a bit like that. how are we doing better than the bounds? the mathematical reason is because we're cheating. it's because we have training data, and these bounds were not formulated under the assumption of training data. that training data in a sense is guiding me to a better local point quicker.

[[40:31](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=2431)] - dimensionality of the problems? the beauty of it is that it's insensitive to all of that.

[[44:39](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=2679)] - freed parameters in ultrasound applications

qs: the parameter you freed is lambda?  
ans: no, so here we freed everything. because here we know the model is a bad model... modeling it like low rank and sparse, you know it's a human body, all this other stuff goes on. So we freed everything, but still it's only 10 layers, so at the end there's not a lot of parameters to learn.

[[53:38](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=3218)] - Lipschitz Bounds and Generalization

qs: I was interested in the Lipschitz bounds... could you talk a little bit about that because that was about generalization?  
ans: for the structured networks, the generalization error decays exponentially with the number of layers, which is intuitive. but it's nice that we could actually formally prove that it decays... we can show that our bound is smaller than the equivalent bound in general of a standard network. it depends on the soft threshold that we use, but if we choose it appropriately then it's always going to be smaller than the equivalent bound for an equivalent network.

[[56:04](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=3364)] - MATLAB vs. Python; just preference

[[58:01](https://www.youtube.com/watch?v=Z4AIMQCNbq8&t=3481)] - tied vs. untied layers

qs: when you talk about unfolding, you have these different layers. are these different layers trained on the same data?  
ans: there are two versions of this - what we call tied or untied. tied → parameters over layers, making them fixed over all layers. untied → allow them to be different. as a rule of thumb, untied is better; it gives it more power. but there are exceptions:

→ very small amount of training data, there could be an advantage to tied because I'm learning fewer parameters.

→ if the underlying layers have physical meaning (like an unknown channel also needs to be estimated), there’s a need to tie.

## comparative analysis:  
[https://engineering.purdue.edu/~bouman/Plug-and-Play/webdocs/GlobalSIP2013a.pdf](https://engineering.purdue.edu/~bouman/Plug-and-Play/webdocs/GlobalSIP2013a.pdf)  
[https://proceedings.neurips.cc/paper_files/paper/2019/file/01386bd6d8e091c2ab4c7c7de644d37b-Paper.pdf](https://proceedings.neurips.cc/paper_files/paper/2019/file/01386bd6d8e091c2ab4c7c7de644d37b-Paper.pdf)

plug-n-play (pnp):

classic algo → replace one specific math step (often prior or regularization) with pre-trained nn (like image denoiser)

uses ADMM (Alternating Direction Method of Multipliers) technique to decouple the problem into two distinct steps

step 1 → forward model (handles physics/measurements).

step 2 → prior model (handles denoising).

minimize $f(x) + g(z)$ subject to $Ax + Bz = c$

"plug-n-play" element → swap out the prior model step with a state-of-the-art nn without altering the rest.

advantage: keeps the classical infinite optimization loop and theoretical stability, but uses raw nn power just for the complex denoising math.

deep equilibrium models (deq):

design network to find fixed point (eq state) → convergence

working:

standard au → unrolls for K layers & tracks data layer-by-layer.

deq → unrolls weight-tied network to infinity (k→∞).

infinite depth nn eventually hits a stable equilibrium state.

skips layer-by-layer math entirely → uses root-finding algo to jump straight to the fixed point.

advantage (deals with the memory bottleneck):

- standard au needs to save intermediate math for all K layers for backprop → massive memory drain.
- deq → jumps to root & uses implicit differentiation to backprop analytically.
- result → trains "infinite-depth" nn using only constant O(1) memory.

## vishal monga et al:

applications:

LISTA:

W ↔ A;

overcomplete dictionary: W∈Rn×m has n rows (the dimension of the signal) and m columns (the number of building blocks); m>n

paint set of 3 colors vs 120 colors.  
because an overcomplete dictionary W has redundant columns, there are an infinite number of mathematical ways to combine those columns to equal y. sparse coding algorithms (like ISTA) take advantage of this redundancy to be incredibly picky. they search for the one specific combination that uses the absolute fewest columns possible to approximate y.

loss function: $\ell(\mathbf{W}_t, \mathbf{W}e, \lambda) = \frac{1}{N} \sum{n=1}^{N} \| \hat{\mathbf{x}}^n(\mathbf{y}^n; \mathbf{W}_t, \mathbf{W}_e, \lambda) - \mathbf{x}^{*n} \|_2^2,$

network is trained through loss minimization,  
using popular gradient-based learning techniques, such  
as stochastic gradient descent [18]

DUBLID:

spatially invariant blurring process can be represented as a discrete convolution: $y=k∗x+n$

$y$ is the blurred image, $x$ is the latent sharp image, $k$ is the unknown blur kernel, and $n$ is Gaussian random noise

total variation (tv) minimization

natural images usually have flat regions separated by sharp edges, meaning their gradients (derivatives) are sparse.

![image.png](attachment:e4d55ae4-f163-48c1-8389-922eeb4d0cd7:image.png)

S6 sets up a cost function. It tries to find the kernel $k$ and the image gradients ($g_1$, $g_2$) that best explain the blurred image $y$, while forcing the gradients to be sparse (using the $ℓ1$ norm penalties controlled by $λ_1$, $λ_2$)

![image.png](attachment:3ce5e5fa-cc35-4cac-90c5-6a3aacd1fd28:image.png)

S7 is just a more robust, generalized version of S6. instead of just looking at basic horizontal and vertical derivatives (Dx,Dy), it uses a set of C different linear filters ($f_i$) to extract gradient features.

S7 is computationally brutal because the variables are heavily coupled → hqs used

![image.png](attachment:db5984fe-4e1c-4adf-8e2f-25ba46dc65cf:image.png)

half-quadratic splitting algorithm (hqs):

introduces auxiliary variables ($z_i$). By doing this, it splits into a sequence of simpler, decoupled sub-problems.

the new parameter $ζi$ penalizes the difference between the true gradient $g_i$ and the auxiliary variable $z_i$ → $ζi$ increases, $g_i$ and $z_i$ are forced to be identical

each variable sequentially now has a clean, closed-form analytical mathematical solution → allows casting to network layers.

![image.png](attachment:00aabe57-9653-4833-9917-90aa043669bb:image.png)

updating $g_i$ → involves convolutions (slow in spatial domain) → do fft and take inverse after computing → $M^1$

updating $z_i$ → soft-thresholding operationl; same as ISTA

updating $k$ → uses fft for speed, ReLU operator ([⋅]+) to ensure the kernel values don't go negative, and a normalization operator (N1) to ensure the kernel sums to 1 (energy conservation).

$ζ′$, $β′$, $f′$ → now learnable weights

refined estimates for the image gradients ($g_L$) and the blur kernel ($k_L$) are obtained after $L$ iterations. image is retrieved by solving least squares problem

filter coefficients ($f_L$) used in this final retrieval step are shared with the intermediate layer updates. because the entire mathematical process is unrolled into a differentiable pipeline, all of these parameters are updated jointly.

training loss function is the translation-invariant mean-  
square-error loss to compensate for the possible spatial shifts of the deblurred images and the blur kernel.

![image.png](attachment:706e6f31-757e-4621-b725-1efe20c9b0c4:image.png)

![image.png](attachment:047071f3-8183-4caa-b86d-8e1351c18d9a:image.png)

ADMM-CSNet:

compressed measurements $y$ and want to rebuild the original signal $x$. $D_i$ acts as a linear filter (like a wavelet transform) to make it sparse (like ISTA)

![image.png](attachment:6a2652f6-4f17-4ca4-8e4d-cdfb00a124de:image.png)

introduce new variable $z_i$ and force it to equal $D_ix$.

![image.png](attachment:cad7e1bd-d484-43a5-816a-e3a3101332e0:image.png)

S13 is the Augmented Lagrangian

![image.png](attachment:2ce52cd8-11fc-4f7e-a6de-ff77f939b976:image.png)

differs from hqs: it doesn't just penalize the difference between $z_i$ and $D_ix$. it introduces dual variables ($α_i$) that act like an accumulator, tracking the constraint violation over time to force exact convergence.

![image.png](attachment:7637decd-200a-4ed4-930e-4230714df78b:image.png)

U1 → (updating $x$): linear solver that aggregates the current guesses and does matrix inversion to find the best signal fit.

U2 → (updating $z$): non-linear step that uses a proximal mapping operator Pg (operates identically to the soft-thresholding) to enforce sparsity.

U3 → (updating $α$): simple gradient ascent update for the dual variable based on the current error ($D^l_ix−z_i^l$).

unlike trad ADMM, doesnt have manually guess the exact right penalty coefficients ($ρ$), step sizes ($η$), and regularization weights ($λ$) → learnt, even learns transform matrices $D_i$ (which would’ve been standard wavelets)

![image.png](attachment:1a2d7b2e-3b7f-49ea-b789-3bff10e58b32:image.png)

CORONA:

ultrasound captures frames with echoes from two main things: static tissue and moving blood

data matrix $D$ → columns of frames

$D=H_1L+H_2S+N$

$L$ (tissue signals) → columns of are highly correlated as bg tissue barely moves → $L$ is low rank matrix  
$S$ (blood echoes) → blood vessels only take up a tiny fraction of the physical space in the imaged medium → $S$ is highly sparse

$H_1$, $H_2$ → measurement matrices

$N$ → noise.

![image.png](attachment:3da8abbc-bf46-4085-aee2-d7463fde7135:image.png)

optimisation problem is to extract S and filter out L

$∥L∥∗$ : nuclear norm → promotes low rank solutions; acts as a proxy to minimize the rank of the matrix, forcing the $L$ to be highly correlated and static

$∥S∥_{1,2}$ is the mixed $ℓ_{1,2}$ norm → enforces row-wise sparsity for $S$

it works like ISTA but built for matrices.

![image.png](attachment:9ad53848-9133-4624-91a7-a534ded27203:image.png)

two different penalties exist → two operatprs for thresholding

$T_λ${⋅}: singular-value thresholding operator.

computes the SVD of the matrix, soft-thresholds the singular values, and rebuilds the matrix. This is the proximal mapping for the nuclear norm (enforcing low-rank).

$S_λ^{1,2}${⋅}: row-wise soft-thresholding operator.

it is the proximal mapping for the mixed norm (enforcing sparsity).

running SVDs and massive matrix multiplications (H1TH1) iteratively on a sequence of high-res ultrasound frames → computationally expensive

→ unrolled into cnn

matrix multiplications → convolutions

fixed matrices ($H_1$, $H_2$) are replaced by a series of small, learnable convolution filters ($P_1^l$ through $P_6^l$).

thresholding parameters ($λ_1^l$, $λ_2^l$) are also learned layer by layer.

learns the optimal convolution filters ($P$) and thresholds ($λ$) via backpropagation, using ground truth matrices $L$ and $S$ (generated slowly by the traditional algorithm) to calculate the mean-square-error loss.

CRF-RNN:

application is semantic segmentation (classification of every single pixel → where dog ends and grass begins)

standard dl nn → guess label and assign; crf → more holistic; assigns labels by looking at a pixel and its relationship with its neighbors

special case of crf → Markov random field; only pairwise interactions of graph are considered

![image.png](attachment:d4402653-09b6-4161-8445-dea309f6944f:image.png)

done by minimising energy, consisting of two parts:

unary energy ($ϕ_p$):

raw, independent guess. what label does pixel $p$ look like based solely on the image data?

this initial guess is usually chosen as the output of a semantic segmentation network, generated by a standard fully convolutional network (FCN).

![image.png](attachment:678a2dad-ea59-4690-b01e-357b722c066b:image.png)

pairwise energy ($ψ_{p,q}$):

smoothness constraint.

if pixel $p$ and a neighboring pixel $q$ have similar colors or features, they should probably be assigned the same label.

this term penalizes the model for mismatched labels on similar adjacent pixels, which helps sharpen the edges of objects.

Mean-Field (MF) iteration algorithm is used to solve energy equations and find optimal pixel labels → iterates through 4 steps

![image.png](attachment:e659706a-db33-46fa-a943-4128d69199f2:image.png)

message passing (S18): compares pixel features using gaussian kernels → acts exactly like passing the data through a convolutional layer.

compatibility transform (S19): evaluates how compatible different labels are with one another → translates perfectly to 1×1 convolution.

unary addition: add the original raw guess ($ϕ_p$) from the FCN back → element-wise addition.

normalization: convert the final scores into proper probabilities → same as softmax layer.

maps cleanly to network layers → connected together in loop to form an RNN.

as entire process is now built from differentiable network layers, the FCN and the CRF-RNN can be trained together end-to-end

raw image goes into an FCN to get the rough, blurry label estimates ($ϕ$) → rough estimate, along with the raw image, is fed into Stage 1 of the CRF-RNN → RNN loops through the unrolled steps (4 above) → after a set number of stages, it outputs the final, sharp predicted labels

![image.png](attachment:0239c455-993d-4c8e-a766-ecd2c6d8cf07:image.png)

Deep (Unrolled) NMF:

applied in single-channel source separation (task of decoupling several source signals from their mixture)

audio processing: convert it into a spectrogram → massive matrix M where the rows are frequencies and the columns are time frames.

NMF → approximates $M≈WH$; where $W$, $H$ are smaller matrices

$W$→ isolated, fundamental spectral signatures of the source

$H$→ tracks the coefficients over time. tells you when and how loud that note is playing.

audio energy is strictly positive physically → W≥0, H≥0 → keeps it physically meaningful

![image.png](attachment:ad4b9f3a-800f-4a00-9c9b-f9a7211f75de:image.png)

S22 is the cost function → tries to minimize the difference between the true mixture $M$ and the reconstruction $WH$.

measures this difference using the $β$-divergence ($Dβ$) → generalization of the well-known Kullback–Leibler  
divergence (common distance metric in audio processing)

also adds an $ℓ1$ penalty to $H$ because sound sources are usually sparse (notes arent playing continuously)

standard gradient descent subtracts values, which could push $W$ or $H$ into negative numbers → multiplicative updates used to solve

![image.png](attachment:1e9f3936-490d-4fb8-acd4-2797ed145a64:image.png)

S23: updates $H$ by multiplying the old guess by a structured ratio.  
S24: updates $W$ using a similar ratio.  
S25: normalize $W$ so that columns have unit norm and scale $H$ accordingly

instead of forcing the network to calculate the massive, complex update rule for $W$ (S24) in every single layer → simply untie $W$ from the iterative loop completely.

$W$ is now standard, trainable weights of the neural network itself → only $H$ update (S23) and the normalization (S25) is executed in loops

$M$ → $L$ layers to figure out the activations $H$ → standard backpropagation → learns the absolute best sound dictionary $W$ based on the $β$-divergence training loss

Neural Network Training Using EKF:

kalman filter

fundamental techniwue that has wide range of applications.

obtains the MMSE estimation of system state by recursively drawing observed samples and updating estimate

extended kalman filter (EKF)

extends to non-linear case through iterative linearization

neural network training is essentially a parameter estimation problem → ekf can be used

the "state" of our system is the weights (wk). the "time step" k is just the arrival of a new training sample.

estimate the true state of the weights based on a sequence of noisy observations.

![image.png](attachment:f75aa081-e0c6-4306-a420-054bd2a8bed6:image.png)

S26 (state transition): $w_k+1=w_k+ω_k$.

this says the true weights at the next step are the same as the current step, plus some artificial process noise ($ω_k$).

they inject this noise on purpose so the math doesn't get overly confident and stuck in a local minimum.

S27 (observation model): $y_k=h_k(x_k;w_k)+v_k$.

the observation" is the target label $y_k$.

the network $h_k$ is the sensor trying to observe that target, and $v_k$ is the measurement noise.

neural networks are inherently nonlinear whereas kalman filters only work on linear systems → ekf used

![image.png](attachment:ec380b61-dace-4d61-a8f5-14ece8d211ac:image.png)

ekf → linearizes the network at the current time step using a first-order Taylor series expansion

$H_k$ in that equation is the Jacobian; the derivative of the network's output with respect to its weights

backprop is still used but not to update the weights but rather as a tool to calculate $H_k$ for the Kalman filter.

instead of using simple learnign rate alpha, three matrix multiplications are carried out to find and update weights for each new data point

![image.png](attachment:e2dfb9b6-ed59-4c59-879b-5b356718e890:image.png)

calculate the Kalman Gain ($K_k$): This dynamically calculates exactly how much we should trust our new observation versus our previous weight estimates.

update the state ($w^k+1$): We adjust the weights based on the prediction error ($y_k−y^k$), scaled by that Kalman Gain.

update the error covariance ($P_k+1$): We update our uncertainty about the weights.

why?

instead of standard SGD or Adam

→ due to covariance matrix $P_k$

standard backprop only looks at the first derivative (the slope).

$P_k$ tracks the correlations between all the different network parameters, which gives the algorithm second-order derivative information (the curvature of the loss space).

because it understands the curvature, it can take massive, highly accurate steps, cutting down the number of training epochs required by orders of magnitude.

catch:

storing an $N×N$ covariance matrix for a modern deep network is computationally impossible → have to use tricks to chop $P_k$ into smaller block-diagonal chunks to make it run

![image.png](attachment:8c2947df-da92-4eeb-8bd9-287eecfa50ac:image.png)

convergence and optimality analysis of LISTA

initially there were the following unknowns

didn't know if LISTA was strictly better than standard ISTA

its convergence rate was unknown

initial matrix substitutions seemed totally arbitrary

didnt know what the optimal learned parameters physically represented

iterative hard tresholding algorithm (IHTA)

![image.png](attachment:cf7f0a99-e907-401f-bef7-e711fe043db1:image.png)

S30 → instead of the $ℓ1$ norm (which pushes values toward zero softly), it uses the $ℓ0$ norm ($∥x∥0≤k$); hard limit: it counts the non-zero entries and forces the network to only keep the top k values.

![image.png](attachment:39814eaf-8e60-41a6-98c8-e9c90f4a53eb:image.png)

S31 → the unrolled layer for this hard-thresholding setup.

![image.png](attachment:8aaf2330-ad30-4103-b480-62eb18e6bc1d:image.png)

S32 → proved that for this network to successfully recover the signal, the learned weight matrix $W_1$ must take the form $I−ΓW$

if the network is trained well, it will naturally find some matrix Γ that works → explains initial substitution in LISTA ($W_1=I−(1/μ)W^TW$)

→ don't need the exact initial substitution for the network to succeed

asymptotic weight coupling

as the network gets incredibly deep ($l→∞$), the weights $W^l_t$ and $W_e^l$ stop being random and get fixed: $W_t^l−(I−W_e^lW)→0$

decided that since network naturally reaches there, it could be forced from the start to be $W_t^l=I−W_e^lW$

forcing this coupling → able to mathematically prove that the unrolled network achieves a linear convergence rate.

analytically found weights

proved that you don't actually need to use backpropagation and training data to learn the optimal weights $W_e^l$

why?

if analytically calculated weights are used, a completely untrained, pure-math unrolled network is just as fast and efficient as a deep learning model that spent hours training on GPUs → significantly reduces parameters to learn

| Network & Application                     | Original Problem                                                          | Classic Iterative Algorithm                        | What Gets Unrolled?                                            | What Becomes Learnable?                                                              | Physical Prior                                                                           | Why Unrolling Works Here                                                                                                                    | Core Advantage                                                                                        |
| ----------------------------------------- | ------------------------------------------------------------------------- | -------------------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| SCN _(Single Image Super-Resolution)_     | Recover a high-resolution image from a low-resolution observation.        | Sparse Coding (LISTA)                              | Sparse coding iterations become network layers.                | Dictionary atoms and sparse reconstruction mappings.                                 | High-resolution image patches admit sparse representations in an appropriate dictionary. | Instead of solving a sparse coding problem for every patch, the network learns a direct approximation to the sparse reconstruction process. | Faster and more accurate super-resolution than iterative sparse coding; preserves edges and textures. |
| DUBLID _(Blind Image Deblurring)_         | Recover both the sharp image and unknown blur kernel from a blurry image. | Half-Quadratic Splitting (HQS)                     | Each HQS stage becomes a network layer.                        | Learnable filters ($f_i$), regularization parameters, and reconstruction operators.  | Natural images are piecewise smooth with sparse gradients (generalized TV prior).        | Replaces handcrafted image priors with data-driven priors while retaining the physical blur model.                                          | ~1000× faster than classical TV-based blind deblurring and produces sharper reconstructions.          |
| ADMM-CSNet _(MRI / Compressive Sensing)_  | Reconstruct images from undersampled measurements.                        | Alternating Direction Method of Multipliers (ADMM) | ADMM variable-update steps become network stages.              | Sparsifying transforms ($D_i$), thresholds, and penalty parameters.                  | Variable splitting separates measurement physics from image structure priors.            | The network learns how sparsity should be enforced rather than relying on manually designed transforms.                                     | Achieves comparable quality with less sampling and much faster reconstruction.                        |
| CORONA _(Ultrasound Clutter Suppression)_ | Separate blood flow signals from highly correlated tissue clutter.        | Robust PCA / Matrix ISTA                           | Low-rank + sparse decomposition iterations become layers.      | Convolutional filters replacing expensive matrix operations (e.g., SVD-heavy steps). | Tissue echoes are low-rank; blood signals are sparse.                                    | Learns efficient approximations to computationally expensive matrix decompositions.                                                         | Large computational savings while preserving clutter suppression performance.                         |
| CRF-RNN _(Semantic Segmentation)_         | Assign consistent semantic labels to image pixels.                        | Mean-Field Inference for Conditional Random Fields | Mean-field message-passing iterations become recurrent layers. | Compatibility transforms and filtering operations.                                   | Nearby pixels with similar appearance should share labels.                               | Preserves structured prediction while allowing end-to-end CNN training.                                                                     | Produces smoother, more globally consistent segmentations.                                            |
| Deep NMF _(Speech / Source Separation)_   | Separate mixed audio sources into interpretable components.               | Non-negative Matrix Factorization (NMF)            | Multiplicative update iterations become network layers.        | Basis dictionary ($W$) and reconstruction parameters.                                | Spectral energy is non-negative; sources are sparse and additive.                        | Learns data-specific spectral dictionaries instead of repeatedly solving constrained factorization updates.                                 | Faster source separation and better adaptation to real-world audio data.                              |
| LISTA _(Sparse Recovery)_                 | Recover sparse signals from measurements.                                 | ISTA                                               | Gradient + soft-thresholding iterations become layers.         | ($W_1$), ($W_2$), thresholds, and optionally layer-specific parameters.              | Signals are sparse in a suitable representation.                                         | Learns a data-driven approximation to the optimization trajectory, effectively preconditioning the problem.                                 | Reaches ISTA-level accuracy in dramatically fewer iterations/layers.                                  |
