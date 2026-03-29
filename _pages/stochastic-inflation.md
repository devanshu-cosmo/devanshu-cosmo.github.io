---
layout: single
title: "Stochastic Inflation and Numerical Simulations"
permalink: /research/stochastic-inflation/
---

## Background

Moments after the Big Bang, cosmic inflation, a beautiful theory explaining the origin of primordial perturbations, took over. During inflation, the vacuum energy fluctuations are passed on as quantum fluctuations in a scalar field that drives inflation (the inflaton field). Interestingly, the same quantum fluctuations give rise to classicalized entities such as galaxies, halos, and the Cosmic Microwave Background. 

This quantum-to-classical transition occurs when perturbations in the inflaton field are stretched to super-Horizon scales. This is where the beauty of stochastic processes enters the picture. The quantum perturbations don't just classicalize at large scales; they also "kick" the homogeneous background (the unperturbed universe) and render stochasticity to it.

This is the idea behind **stochastic inflation**. 

---

## The Math

The inflaton field, which was supposed to follow a smooth evolution, now follows **Langevin Equations** imitating the famous Brownian motion:

$$
\frac{d\phi}{dN} = \pi + \xi_\phi, \qquad
\frac{d\pi}{dN} = - (3 - \epsilon)\pi - \frac{V_{,\phi}}{H^2} + \xi_\pi
$$

Here, the terms \(\xi_\phi\) and \(\xi_\pi\) represent stochastic noise arising from quantum fluctuations that continuously source the large-scale dynamics. One can extract interesting statistical information by collecting numerous random realizations of a single inflation model. 

In cosmology, one needs to evaluate "observables" in order to match theoretical predictions with observations. One such class of observables is statistical correlators of primordial curvature perturbations (\( \mathcal{R} \)). A powerful idea called the \( \Delta N \) formalism connects the stochastic formalism to curvature perturbations:

$$
\mathcal{R} = \Delta N
$$

Hence, instead of solving complicated equations from Quantum Field Theory, we can build stochastic simulations to get to the statistical correlators (two-point function, three-point function, etc) of \(\mathcal{R}\). 

---

## The Simulations

The simulations involve repeated but independent evolution of the universe throughout the inflationary phase, while recording snapshots of intermediate states as well as the first-passage time. The latter is done for the end of inflation to get statistics of the duration of inflation \(\Delta N\). 

Repeating this process for **millions of realizations** allows us to construct probability distributions (PDFs) and power spectra of curvature perturbations. This is what my project precisely does. At the computational level, my algorithm:

- Implements stochastic differential equations using Stochastic Runge–Kutta methods
- Discretizes the field trajectory into a matrix of space and time to catch the snapshots of the inflaton field's phases
- Tests the model accuracy across several sensitive hyperparameters, including initial conditions and fine-tuning the space-time grid
- Adds **importance sampling** to the random noise sampler to catch the **very rare** realizations sitting far in the tail of the PDF
- Executes parallelized simulations on the **RWTH Aachen computing cluster** across **100+ CPU cores**

The picture below helps you imagine what goes on inside a simulation that is trying to catch the stochastic evolutions of a scalar field, just \(10^{-36}\) seconds after the Big Bang!

---

![Stochastic simulation workflow](/assets/images/figure7_stochastic_inflation.jpg)

*An ensemble of stochastic trajectories of the field illustrating how quantum noise perturbs the field trajectory and generates a spread in outcomes, with red dots being the mean of the spread.*

---

## Results & Summary

The model provides an algorithm to quantify the effects of stochastic processes on the primordial curvature perturbations that later classicalized and produced all the structure in the late universe. Our algorithm maps the real-space statistics to more intuitive Fourier-space statistics, such as the power spectrum, bispectrum, etc. 

We can refine the tail statistics of the perturbations, which is crucial for the formation of **Primordial Black Holes**. In doing so, we avoid relying on complicated perturbative or quantum field theoretic calculations.

Furthermore, the algorithm is readily extendable to multi-field inflation models and, possibly, to a stochastic formalism for tensor perturbations. 

---

![Algorithm flowchart](/assets/images/Stochastic_Inflation_algorithm_flowchart.jpg)

*Flowchart of the stochastic simulation algorithm, from noise generation to reconstruction of statistical observables.*

If you have an idea to build upon this framework, either in physics or outside of it, do not hesitate to contact me!

---

## Reference

**Stochastic inflation and non-perturbative power spectrum beyond slow roll**  
[arXiv:2411.08854](https://arxiv.org/abs/2411.08854)
