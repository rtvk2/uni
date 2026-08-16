
  C3

### 1. Basic Intensity Transformations (Point Processing)

These operate on single pixels without caring about the surrounding image. If $r$ is the input pixel intensity and $s$ is the output, and $L$ is the number of intensity levels (usually 256).

|**Transformation**|**Formula**|**What it does / Mental Trigger**|
|---|---|---|
|**Image Negative**|$s = (L - 1) - r$|Reverses the grayscale. Dark becomes light, light becomes dark. Perfect for highlighting white detail embedded in massive dark regions (like medical X-rays).|
|**Log Transform**|$s = c \log(1 + r)$|Expands dark pixels while heavily compressing bright pixels. The $+1$ prevents $\log(0)$. This is exactly what you use to make the Fourier transform magnitude visible on a screen.|
|**Power-Law (Gamma)**|$s = c r^\gamma$|If $\gamma < 1$, it acts like a Log transform (brightens). If $\gamma > 1$, it compresses darks and expands brights (darkens). Monitors inherently apply a gamma > 1, so "Gamma Correction" applies $\gamma < 1$ to fix it.|
|**Contrast Stretching**|Piecewise linear $T(r)$|Stretches a narrow range of intensities to span the full $[0, L-1]$ scale. Saves washed-out images with poor lighting.

### 2. Histogram Processing

This relies on the Probability Density Function (PDF) of the image, where $p(r_k) = n_k / MN$ ($n_k$ is the count of pixels with intensity $r_k$, $MN$ is total pixels).
  
|**Operation**|**Formula**|**What it does / Mental Trigger**|
|---|---|---|
|**Histogram Equalization**|$s_k = (L-1) \sum_{j=0}^{k} p(r_j)$|Forces the image to utilize every available intensity level equally. It uses the Cumulative Distribution Function (CDF) to flatten the histogram, maximizing global contrast automatically.|
|**Histogram Matching (Specification)**|$G(z_q) = s_k \rightarrow z_q = G^{-1}(s_k)$|When equalization is too aggressive. You compute the equalized histogram of your image ($s_k$), compute the equalized histogram of a _target_ shape ($G(z_q)$), and map them together so your image matches the target.|

### 3. Smoothing Spatial Filters (Lowpass)

These use a neighborhood mask (kernel) that slides over the image. They blur the image to remove noise or irrelevant detail.

| **Filter Type**                        | **3x3 Mask Example**                                                             | **What it does / Mental Trigger**                                                                                                                                                             |
| -------------------------------------- | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Standard Box Filter**                | $\frac{1}{9} \begin{bmatrix} 1 & 1 & 1 \\ 1 & 1 & 1 \\ 1 & 1 & 1 \end{bmatrix}$  | The simplest local average. It blurs everything equally. Brutal on sharp edges.                                                                                                               |
| **Weighted Average (Gaussian approx)** | $\frac{1}{16} \begin{bmatrix} 1 & 2 & 1 \\ 2 & 4 & 2 \\ 1 & 2 & 1 \end{bmatrix}$ | Gives more weight to the center pixel. Creates a much smoother, more natural blur than the box filter without destroying edges as aggressively.                                               |
| **Median Filter (Nonlinear)**          | _Sort neighbors, pick middle_                                                    | The absolute best tool for "Salt and Pepper" (impulse) noise. Because it picks an actual existing pixel value rather than averaging, it removes extreme dots while keeping edges razor sharp. |

### 4. Sharpening Spatial Filters (Highpass)

Smoothing is integration (averaging); sharpening is differentiation. These highlight intensity transitions (edges).

|**Filter Type**|**3x3 Mask Example**|**What it does / Mental Trigger**|
|---|---|---|
|**Laplacian** (2nd Derivative)|$\begin{bmatrix} 0 & 1 & 0 \\ 1 & -4 & 1 \\ 0 & 1 & 0 \end{bmatrix}$|Isotropic (directionless). It finds fine detail and ignores slow gradients. To sharpen, you subtract the Laplacian result from the original image: $g(x,y) = f(x,y) - \nabla^2 f(x,y)$.|
|**Laplacian** (w/ Diagonals)|$\begin{bmatrix} 1 & 1 & 1 \\ 1 & -8 & 1 \\ 1 & 1 & 1 \end{bmatrix}$|Same as above but includes diagonal neighbors for a stronger, sharper response.|
|**Sobel $x$** (1st Derivative)|$\begin{bmatrix} -1 & -2 & -1 \\ 0 & 0 & 0 \\ 1 & 2 & 1 \end{bmatrix}$|Highlights horizontal edges. The 2s in the middle row provide slight smoothing to suppress noise while taking the derivative.|
|**Sobel $y$** (1st Derivative)|$\begin{bmatrix} -1 & 0 & 1 \\ -2 & 0 & 2 \\ -1 & 0 & 1 \end{bmatrix}$|Highlights vertical edges. Combine magnitude with Sobel $x$ to get the full gradient edge map: $M(x,y) \approx \vert{}G_x\vert{} + \vert{}G_y\vert{}$.|
|**Unsharp Masking**|$g_{\text{mask}} = f(x,y) - \bar{f}(x,y)$|Old darkroom trick. Blur the image ($\bar{f}$), subtract the blur from the original to get a "mask" of just the edges, then add that mask back to the original to sharpen it.|
|**Highboost Filtering**|$g(x,y) = f(x,y) + k \cdot g_{\text{mask}}$|A generalized version of Unsharp Masking. If $k=1$, it is unsharp masking. If $k>1$, it heavily emphasizes the edges.|