---
layout: single
title: "CMB Spectral Distortions & non-Gaussian Statistics"
permalink: /research/spectral-distortions/
---

## Background

Often assumed to be a perfect blackbody, the Cosmic Microwave Background (CMB) has many reasons to deviate from its pure blackbody behaviour, from external energy injection to inefficient equilibrium-maintaining processes.

Such minute but significant deviations, called **spectral distortions**, carry information about various cosmological events from very early times (post-inflation) to very late times (large-scale structures).

In the early universe, the **dissipation of acoustic waves** in the photon–baryon plasma before recombination. Small-scale primordial fluctuations get erased over time, and their energy is injected back into the photon fluid.

This process, also called **Silk Damping** leaves behind spectral distortions in the CMB, which probe scales much smaller than those probed by the temperature anisotropies in the CMB.

---

## The Current Picture & The Extension

Spectral distortions can help us probe inflationary perturbations, because:

- Primordial fluctuations from inflation (scalar fluctuations in this context) generate sound waves in the early Universe  
- These waves get damped due to photon diffusion across different scales 
- The diffusing photons cause mixing of slightly different blackbodies, producing spectral distortions.

In practice, the distortion signal can be written as:

$$
\mu = \int \frac{dk}{k} \, \mathcal{P}_{\mathcal{R}}(k) \, W_\mu(k)
$$ 

Hence, by observing or not observing the spectral distortions from experiments, we can get insights about one of the oldest observables in the universe, viz. the **primordial curvature power spectrum** $\mathcal{P}_{\mathcal{R}}(k)$.

Most analyses assume that primordial fluctuations are **Gaussian**. Primordial Gaussianity has been verified at the CMB scales; however, here we are talking about much smaller scales, especially in scenarios that produce **Primordial Black Holes (PBHs)**. We expect non-Gaussianity to play a crucial role at such small scales.

> My research focuses on the question: How does the mapping between the CMB spectral distortions and constraints on the primordial perturbations change when the perturbations follow **non-Gaussian** statistics?

![CMB distortion kernel](/assets/images/fig1_spectral_distortions.jpg)

*Only a narrow range of scales and redshifts contribute significantly to μ-distortions and y-distortions. This figure shows where most of the signal is generated in the early Universe.*

---

## Methodology

The primary challenge is to devise a statistical framework that would include all the important terms of primordial perturbations contributing to **additional energy injection** to the photon thermal bath.

We can keep track of the injected energy in the photon bath by measuring the **spatial anisotropy moments** of the CMB.

Including primordial non-Gaussianity changes how much distortion can be produced by the statistical correlators of perturbations, because:

- The signal is no longer controlled only by the power spectrum  
- **Three-point correlations (bispectrum)** of photon anisotropy moments cannot be ignored  
- The heating of the photon fluid receives additional corrections that were ignored in the standard literature

A key takeaway from the figure above is that:

- μ-distortions are generated roughly between  
  **$10^4 \lesssim z \lesssim 10^6$**  
- Only specific ranges of wavelengths contribute  

---

## Technical Execution

This project sits at the intersection of analytical calculations, implementing a statistical framework into a production-level code, and data interpretation.

At the technical level, I:

- Derived the **heating rate** of the photon fluid, including higher-order (non-Gaussian) contributions  
- Evaluated **multi-dimensional integrals** involving correlation functions in Fourier space  
- Built mappings from theoretical models to observables like μ-distortions  
- Extended and modified the **CLASS Boltzmann code** to handle these calculations efficiently  
- Tested different approximation schemes and ensured numerical stability across scales  

An important bottleneck was to deal with **highly oscillatory integrals** over both momentum space and cosmological time, while keeping the computation fast and accurate.

---

## Results

![Non-Gaussian effects](/assets/images/fig6_spectral_distortions.jpg)

*Non-Gaussianity can significantly change the constraints on primordial fluctuations, especially when the deviations from Gaussianity are large.*

I summarize the **key outcomes** of this project below:

- For weak non-Gaussianity, results remain close to the standard (Gaussian) case  
- For strong non-Gaussianity, constraints can become **much tighter**  
- The impact depends strongly on:
  - the scale of the fluctuations  
  - the strength of non-Gaussianity  

More importantly, we find that non-Gaussianity becomes crucial if one has to study small-scale primordial perturbations and the entities resulting from them (PBHs, SIGWs, etc), and the mathematics becomes much more challenging, thanks to higher-order statistical correlators contributing strongly.

Our paper also provides numerical fits for the non-Gaussian window functions for distortions, in case people want to use our results without having to work with the CLASS code directly.

## Reference

**Spectral distortions from acoustic dissipation with non-Gaussian (or not) perturbations**  
[arXiv:2404.18474](https://arxiv.org/abs/2404.18474)
