# Early Signs of Instability

Many dynamical systems change abruptly as a control parameter is varied: the Rayleigh parameter in Lorenz flow, forcing strength in Duffing dynamics, fuel–air ratio in a combustor, or airspeed for a flexible wing.

This project asks a simple question:

> **Can an approaching transition be detected from short records, before large oscillations appear or Lyapunov-based methods have enough data?**

## Core idea

Let $\theta$ denote the control parameter.

At each value of $\theta$, we observe the system state $X_\theta$, estimate its mean $\mu_\theta$ and covariance $\Sigma_\theta$, and approximate the local state distribution by the Gaussian with the same first two moments,

$$
X_\theta \approx \mathcal{N}(\mu_\theta,\Sigma_\theta).
$$

We then measure how rapidly this Gaussian surrogate changes as $\theta$ changes. The covariance contribution to the KL/Fisher curvature is

$$
\kappa(\theta)
=
\frac{1}{2}
\operatorname{tr}
\left[
\left(
\Sigma_\theta^{-1}\Sigma_\theta'
\right)^2
\right].
$$

A large value of $\kappa(\theta)$ means that a small change in the control parameter produces a large change in the local covariance structure.

This is different from:

- monitoring variance alone;
- waiting for a large-amplitude response;
- estimating a Lyapunov exponent from a long recurrent trajectory.

The method uses **parameter sensitivity of the local probability law** as the warning signal.

## Theory

For a noisy dominant mode approaching loss of stability, linear theory predicts

$$
\kappa(\theta)\propto(\theta_c-\theta)^{-2}.
$$

This growth does **not** continue indefinitely. When nonlinear effects become important, finite noise cuts off the linear divergence and the sensitivity rolls over.

For the cubic critical-mode model, the exact stationary distribution remains uniformly close to its variance-matched Gaussian throughout the nonlinear crossover:

$$
\sup_\theta
D_{\mathrm{KL}}
\left(
P_\theta
\Vert
\mathcal{N}\!\left(0,\operatorname{Var}P_\theta\right)
\right)
\le 0.031692
\quad\text{nats}.
$$

Thus the Gaussian approximation can remain useful even when the linear variance law has already failed.

The project also treats finite-sample effects explicitly. The estimated Jeffreys/KL curvature is corrected for covariance-estimation bias, correlated records are described through an effective sample size, and empirical significance is evaluated with block bootstrap and matched surrogate data.

## Experiments

The same idea is tested in five different systems.

### Lorenz

KL covariance sensitivity becomes measurable from a short local segment of the attractor before Rosenstein/Kantz Lyapunov estimators have enough recurrent geometry to stabilize.

### Rössler

The result persists in a single-scroll attractor, showing that it is not a consequence of the two-lobe geometry of Lorenz.

### Forced Duffing oscillator

The method detects strong parameter sensitivity within a fraction of one forcing period, while recurrence-based Lyapunov estimation requires many complete periods.

### Annular combustor

Experimental acoustic data show increasing covariance sensitivity as the combustor approaches thermoacoustic instability.

### Flexible wing

Experimental wind-tunnel data give the clearest causal example.

Using only measurements available through $26\,\mathrm{m/s}$,

$$
K_{24\to26} > K_{20\to24},
$$

with block-bootstrap support, while the large-amplitude response appears later at $28\,\mathrm{m/s}$.

The increase is not explained by total variance alone:

$$
\text{full covariance sensitivity ratio}\approx 2.10,
$$

$$
\text{total-variance sensitivity ratio}\approx 1.28.
$$

Their ratio is about

$$
1.64,
$$

indicating an additional contribution from changing multichannel covariance geometry: anisotropy, correlations, and modal orientation.

The standardized non-Gaussianity of the wing signal changes little between $24$ and $26\,\mathrm{m/s}$, then increases sharply at $28\,\mathrm{m/s}$. This supports the sequence

$$
\text{approximately stable Gaussian shape}
\rightarrow
\text{increased covariance sensitivity}
\rightarrow
\text{large nonlinear response}.
$$

## What the project claims

The proposed statistic is **not a chaos detector** and is not a replacement for the Lyapunov exponent.

The central claim is narrower:

> **Parameter sensitivity of a local covariance structure can become detectable before conventional amplitude, variance-only, or recurrence-based instability indicators become informative.**

The Lorenz, Rössler, and Duffing experiments demonstrate the short-record advantage in chaotic benchmark systems. The combustor and wing experiments show the same mechanism in physical data.

## Current validation

The current empirical program includes:

- finite-difference robustness checks;
- Gaussian-approximation diagnostics;
- block-bootstrap confidence intervals;
- effective-sample-size corrections for correlated observations;
- analytic finite-sample bias correction for covariance divergence;
- matched surrogate nulls;
- decomposition of total covariance sensitivity into overall scale and covariance geometry.

The remaining empirical goal is to apply the same matched statistical protocol consistently across all benchmark systems.

## Why this may matter

Near an instability, a system may become **sensitive before it becomes visibly unstable**.

Estimating that sensitivity can require much less trajectory data than reconstructing long-term dynamical quantities such as Lyapunov exponents. This makes the approach potentially useful for systems where experiments are expensive, operating points are sparse, or only short records are available.

## Status

Research project in active development.

The repository contains theoretical derivations, simulation notebooks, and real-data experiments for Lorenz, Rössler, Duffing, thermoacoustic instability, and flexible-wing flutter.

