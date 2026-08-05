
summer'26:

Title: Algorithm Unrolling: Bridging Iterative Methods and Deep Neural Networks
Description: This project explores algorithm unrolling (unfolding) - a technique that bridges iterative signal processing algorithms and deep neural networks by mapping each algorithm iteration to a network layer. The goal is to build interpretable, parameter-efficient neural networks that incorporate domain knowledge, requiring less training data while achieving competitive performance, with potential applications across various signal and image processing domains.
Initial Objectives: 
1. Literature review of algorithm unrolling methods, like LISTA, ADMM-CSNet, DUBLID and others as covered in Monga et al. (2020) and any additional sources. 
2. Mathematical understanding of core unrolling frameworks, including iterative algorithm formulations, network parameter learning, and back-propagation through unrolled architectures.
3. Initial implementation of an unrolled network architecture and empirical evaluation against baseline iterative methods.

monsoon'26:

Title: Algorithm Unrolling II: Stabilizing Deep Unrolled Networks and Extending White-Box Transformers
Description: Building on the research work done during the previous summer, this phase deals with the "curse of unrolling" - the gradient instability that limits how deep unrolled networks like LISTA can be trained. A similar framework can then be extended to unrolled white-box transformers (like CRATE/CRATE-alpha). The project aims to develop and benchmark a stabilization strategy for deep unrolled architectures, as well as verify and stress-test CRATE-alpha's compression-sparsification (MSSA/ODL) mechanics, ultimately building a stable domain-specific unrolled-attention extension for a physical forward model.
Objectives:
1. Explore the gradient degradation limitation ("curse of unrolling") that limits unrolled architectures at high depth and benchmark the untied LISTA baseline to characterize it. 
2. Analyze various stabilizing techniques like gradient control methods (such as gradient clipping/rescaling, Chen et al., 2022), truncated/windowed backpropagation (Chen et al., 2022), architectural stabilization (Gao et al., 2025), and self-supervised stabilization (Ben Sahel and Eldar, 2024). If there exists a clear gap between methods, adapt the best-performing approach into a targeted adaptive variant (e.g., layer-aware gradient scaling).
3. Implement and verify a small-scale CRATE-alpha model (MSSA + ODL layers) on CIFAR-10, to confirm that the compression/sparsification sub-steps behave as an unrolled two-part iteration the way LISTA's gradient and soft-thresholding steps do. Extend this by stress-testing the blocks under mismatched or ill-conditioned inputs to identify where the current Sparse Rate Reduction formulation strains.
4. Create a physical forward model for a targeted domain and derive the custom MSSA/ODL-equivalent layers for that specific signal environment, establishing the theoretical foundation for a novel, stable white-box transformer.

