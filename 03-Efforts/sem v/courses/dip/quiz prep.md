
# DIP Quiz Relevant Questions (Lectures 1–12)

> [!info] **Scope Summary**
> **Included (Lectures 1–12):** Image Formation, HVS, Intensity Transformations, Spatial Filtering, LSI Systems, Sampling Theorem, Fourier Transforms (1D/2D), DFT/FFT, and Frequency Domain Filtering.
> **Excluded (Later Chapters):** Mathematical Morphology (dilation/erosion/opening/closing) and Image Restoration (Wiener/Inverse Filtering).
> (*) Indicates midsem


---
## 1. `DIP_quiz1_2023_M.pdf`
> [!tip] Full Coverage
> All questions in this paper fall within the scope of Lectures 1–8

- [x] **Q1–Q4:** Image formation, Bayer filter pattern, photosites, quantization, and camera aperture effects
- [x] **Q5–Q6:** Intensity transformations and histogram properties of binary images
- [x] **Q7–Q11, Q13–Q14:** Spatial filtering, neighborhoods (4/8/D), padding calculation, mean / LoG / unsharp masks, and non-linear median filtering
- [x] **Q12:** Linear Shift-Invariant (LSI) system properties
- [x] **Q15:** Fourier transform of standard continuous/discrete functions

---

## 2. `DIP_mid_2023_M.pdf`

### Relevant
- [x] **Q1 (Parts 1, 2*, 3*, 4*):** Spatial filter symmetry, FT of impulse function, band-limited vs. spatial-limited properties, and 2D Fourier rotation property
- [x] **Q2:** Histogram properties and image spatial arrangement invariance
- [ ] **Q3*:** Sampling theorem derivation, Nyquist criterion, and frequency domain loss
- [ ] **Q4*:** Fourier transform of a square pulse and ringing artifacts in ideal low-pass filters (ILPF)
- [ ] **Q5*:** Expressing DFT as matrix multiplication and DFT linearity
- [x] **Q6:** Spatial high-pass filter kernel structure (center impulse / zero-sum)

> [!warning] Excluded
> - **Q7 & Q8:** Mathematical morphology (dilation, erosion, closing)

---

## 3. `DIP_mid_2024.pdf`

### Relevant
- [x] **Q1 (Parts 1, 2*, 3*, 4*):** Difference of Gaussians (DoG) approximation, FT of shifted impulse $\delta(t - \tau)$, aliasing reduction techniques, and homomorphic filtering
- [ ] **Q2*:** Sampling interval condition ($\Delta T > 2/\mu_{\max}$) and frequency domain aliasing corruption
- [ ] **Q3*:** Derivation of the Fourier shift theorem: $\mathcal{F}\{h(t - \tau)\}$
- [ ] **Q4*:** Butterworth low-pass filter formula and band-pass filter construction/cutoffs
- [ ] **Q5*:** Translation/rotation effects on Fourier magnitude vs. phase spectra
- [ ] **Q6*:** Notch-reject and band-pass filtering practical applications

> [!warning] Excluded
> - **Q7 & Q8:** Mathematical morphology (dilation commutativity, opening with 3x3 structuring element)[cite: 2]

---

## 4. `DIP_mid1_2025_M.pdf`

### Relevant
- [x] **Q1 (a, b):** Bilateral filtering equations, range/spatial kernels, and conditions approaching Gaussian filtering
- [x] **Q2 (a, b*):** Spatial finite differences vs. derivative computation using DFT properties
- [x] **Q3* (a, b):** Image sampling, aliasing phenomena during capture/reconstruction, and anti-aliasing techniques
- [ ] **Q4* (a, b):** Fourier transform derivations of standard functions (e.g., triangle function, Gaussian) with magnitude/phase plots
- [ ] **Q5*:** 2D Fourier transform properties and operations

	-> Good questions, try to find/generate similar ones for other methods covered & answer