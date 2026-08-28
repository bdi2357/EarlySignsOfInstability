# Early Signs of Instability

Many systems look normal shortly before they become unstable.

Examples include an aircraft wing approaching flutter, a combustor approaching violent oscillations, a power grid developing an unstable mode, or an ecosystem approaching a tipping point.

Most early-warning methods watch one quantity at a time, such as variance, RMS, or lag-1 autocorrelation.

This project asks a broader question:

> **Can instability first appear in the relationships between several measurements, before any single measurement looks dangerous?**

## Main idea

Suppose a system is measured by several sensors. At each operating condition, their fluctuations are described by a covariance matrix

```math
\Sigma(\theta)
```

where $\theta$ is a physical control parameter such as airspeed, forcing strength, Reynolds number, or fuel mixture.

We measure how quickly this covariance changes as the control parameter changes:

```math
\kappa(\theta)
=
\frac{1}{2}
\mathrm{tr}
\left[
\left(
\Sigma_\theta^{-1}\Sigma_\theta'
\right)^2
\right]
```

A large value means that a small change in operating conditions causes a large change in the pattern of fluctuations.

The key point is that this can happen even when:

- total variance changes little;
- RMS is still small;
- the largest covariance eigenvalue barely changes;
- lag-1 autocorrelation gives little warning.

## What this adds

The novelty is not simply using KL divergence or Fisher information as an early-warning signal.

The contribution is to determine **when the full multivariate fluctuation pattern contains information that standard scalar indicators necessarily lose**.

The theory separates two effects:

1. **Amplitude change** — fluctuations get larger.
2. **Geometry change** — fluctuations rotate or redistribute between different modes.

Variance and RMS mainly see the first. The full covariance method can also see the second.

This matters when instability is caused by several interacting modes rather than one growing mode.

The theory also tells us when the method should **not** help. If one mode dominates the approach to instability, a well-chosen scalar variance can already contain almost all useful information.

## Practical implications

This distinction matters in several central applications.

### Aeroelastic flutter

A wing can become unstable because bending and twisting modes begin to couple.

A covariance-based warning could detect this changing mode geometry before vibration amplitude becomes large.

### Thermoacoustic instability

Dangerous combustor oscillations are created by coupling between pressure waves, heat release, and flow.

The useful warning may therefore be a change in their relationship before pressure RMS becomes large.

### Power grids

An unstable grid oscillation can involve many buses moving together in a particular spatial pattern.

The method can target the emerging multivariate mode instead of waiting for one voltage or frequency signal to become abnormal.

### Ecology

An ecosystem may lose resilience through changing relationships between species even when no single population gives a clear warning.

This gives a way to test whether the important precursor is a collective change rather than a single "sentinel species."

## Why this can improve current early-warning methods

Current methods usually ask:

> "Is variance or autocorrelation increasing?"

This project instead asks:

> **"How much information about the approaching instability is present in the full pattern of fluctuations, and how much is lost when the system is reduced to one number?"**

The practical goals are to:

- detect multivariate instabilities earlier when mode coupling matters;
- identify which combination of sensors is becoming unstable;
- determine when simple indicators are already sufficient;
- design smaller and more informative sensor systems;
- separate true covariance-geometry precursors from ordinary amplitude growth.

## Nonlinear theory

Near a simple instability, linear theory predicts that covariance sensitivity can grow rapidly as the restoring force weakens.

However, this growth cannot continue indefinitely. Nonlinear effects eventually become important and cut off the linear divergence.

For a cubic critical-mode model, the exact stationary distribution remains very close to a Gaussian with the same variance, even through this nonlinear crossover. At the critical limit, the KL distance is

```math
D_{\mathrm{KL}}(P \Vert G_P)
=
0.031692
```

nats, where $G_P$ denotes the Gaussian with the same variance as $P$.

This supports using a local Gaussian description well beyond the strictly linear regime.

## Efficient implementation

A naive method repeatedly estimates large covariance matrices and computes a KL divergence between them.

That can be noisy.

The Fisher-information formulation suggests a more efficient approach: learn the important covariance-change direction during calibration, then monitor a single scalar score that preserves the local information relevant to that direction.

This separates the **information available in the system** from the **statistical cost of estimating it**.

## Current evidence

The project includes tests on:

- Lorenz dynamics;
- Rössler dynamics;
- forced Duffing dynamics;
- the circular restricted three-body problem;
- flexible-wing wind-tunnel data;
- bearing run-to-failure data;
- shell-buckling data.

The flexible-wing experiment currently provides the strongest positive real-data example: covariance structure changes before the large vibration response.

The bearing and shell-buckling experiments are useful negative controls: simple amplitude indicators perform as well as or better than the full covariance method.

That is consistent with the theory rather than a contradiction of it.

## Main scientific question

The project is ultimately about one question:

> **Does loss of stability first appear as amplitude growth, or as a reorganization of fluctuation geometry?**

If the answer is "amplitude growth," classical indicators may already be enough.

If the answer is "geometric reorganization," multivariate KL/Fisher methods can reveal information that scalar early-warning signals miss.

## Status

Active research.

The main open empirical goal is to validate this predicted advantage prospectively in additional physical systems where instability is known to involve mode coupling or covariance rotation.
