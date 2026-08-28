# Early Signs of Instability

Many systems approach instability before any single measured signal becomes large.

Examples include an aircraft wing approaching flutter, a combustor approaching thermoacoustic oscillation, a power grid developing an oscillatory mode, or an ecosystem approaching a tipping point.

Most early-warning methods monitor one quantity at a time, such as variance, RMS, or lag-1 autocorrelation.

This project asks:

> **Can instability first appear in how several measured variables fluctuate together, before any one variable gives a strong warning?**

## 1. What is actually measured?

Fix one operating condition, denoted by $\theta$. For example, $\theta$ could be airspeed.

Assume we have $d$ sensors and record them at $n$ time points while $\theta$ is held approximately constant.

The $i$-th observation is

```math
x_i=(x_{i1},x_{i2},\ldots,x_{id})
```

where $x_{ij}$ is the value of sensor $j$ at time $i$.

For each sensor $j$, compute its sample mean:

```math
\mu_j
=
\frac{1}{n}
\sum_{i=1}^{n}x_{ij}
```

Then compute the sample covariance matrix. Its entry in row $j$, column $k$ is

```math
\Sigma_{jk}
=
\frac{1}{n-1}
\sum_{i=1}^{n}
(x_{ij}-\mu_j)
(x_{ik}-\mu_k)
```

This matrix has a direct interpretation:

- $\Sigma_{jj}$ is the variance of sensor $j$;
- $\Sigma_{jk}>0$ means sensors $j$ and $k$ tend to move above and below their means together;
- $\Sigma_{jk}<0$ means they tend to move in opposite directions;
- $\Sigma_{jk}\approx0$ means there is little linear co-fluctuation between them.

So $\Sigma$ contains both the variance of each sensor and the pairwise covariances between sensors.

## 2. How is sensitivity to the control parameter computed?

Repeat the covariance calculation at several nearby values of the physical control parameter.

For example, with measurements at $\theta-h$, $\theta$, and $\theta+h$,

```math
\Sigma'(\theta)
\approx
\frac{
\Sigma(\theta+h)-\Sigma(\theta-h)
}{
2h
}
```

This estimates how every variance and covariance changes per unit change in $\theta$.

A raw derivative is not enough because sensors can have different units and very different variance levels. We therefore normalize by the current covariance.

If

```math
\Sigma=Q\Lambda Q^T
```

where the columns of $Q$ are covariance eigenvectors and the diagonal entries of $\Lambda$ are positive eigenvalues, then

```math
\Sigma^{-1/2}
=
Q\Lambda^{-1/2}Q^T
```

rescales each principal fluctuation direction by its current standard deviation.

Define

```math
A(\theta)
=
\Sigma(\theta)^{-1/2}
\Sigma'(\theta)
\Sigma(\theta)^{-1/2}
```

and then

```math
\kappa(\theta)
=
\frac{1}{2}
\sum_{j,k}A_{jk}(\theta)^2
```

This is the covariance contribution to Fisher information.

For nearby parameter values, the Gaussian KL divergence satisfies

```math
D_{\mathrm{KL}}
\bigl(
P_\theta
\Vert
P_{\theta+\Delta\theta}
\bigr)
=
\frac{1}{2}
\kappa(\theta)
(\Delta\theta)^2
+
o\bigl((\Delta\theta)^2\bigr)
```

when only the covariance contribution is considered.

So a large $\kappa$ has a precise meaning:

> **a small change in the physical control parameter produces a large relative change in the measured variances and cross-covariances.**

## 3. What can this detect that variance alone cannot?

Variance of one signal measures only how much that signal spreads around its mean.

The full covariance matrix also records how different sensors move together.

This matters when instability involves more than one interacting mode.

For example, an aircraft wing can approach flutter because bending and twisting motions become increasingly coupled. The RMS of each sensor may still be moderate while the covariance between bending and torsion measurements changes strongly.

The method is intended for this case.

It is not expected to improve on a scalar variance indicator when one single mode dominates the approach to instability.

More precisely, if the normalized covariance-change matrix $A$ has one eigenvalue much larger in magnitude than all the others, one sensor combination contains almost all covariance information. If several eigenvalues of $A$ have comparable magnitude, more than one independent sensor combination is changing and a scalar variance necessarily loses part of that information.

## 4. Why this can improve current early-warning methods

Standard early-warning methods usually ask:

> Is variance increasing?

or:

> Is lag-1 autocorrelation increasing?

This project asks a different question:

> **How much information about the approaching instability is present in all measured variances and covariances, and how much is lost when they are reduced to one scalar indicator?**

The practical improvement is conditional rather than universal:

- if one mode dominates, a simple scalar warning can already be sufficient;
- if several modes reorganize together, the multivariate covariance can contain warning information that no single variance can retain.

## 5. Where this matters

### Aeroelastic flutter

Flutter can result from coupling between bending and twisting modes.

The method tests whether covariances between strain and acceleration channels change before vibration amplitude becomes large.

### Thermoacoustic instability

Combustion instability is driven by interaction between pressure, heat release, and flow.

The method tests whether their cross-covariances change before pressure RMS gives a strong warning.

### Power grids

An oscillatory grid instability can involve coordinated motion across many buses.

The method uses voltage, phase, and frequency measurements jointly instead of waiting for one bus to become abnormal.

### Ecology

An ecosystem may lose resilience through changing relationships between species.

The method tests whether cross-species covariances change before any single population shows a strong warning.

## 6. Why use a Gaussian approximation?

At each operating point, the method replaces the observed sensor distribution by a Gaussian with the same mean and covariance.

This is an approximation, not an assumption that the real system is exactly Gaussian.

For the cubic critical-mode model studied in this project, the exact stationary distribution remains close to the Gaussian with the same variance even when nonlinear effects become important. At the critical limit,

```math
D_{\mathrm{KL}}(P\Vert G_P)
=
0.031692
```

nats, where $G_P$ is the Gaussian with the same variance as $P$.

This gives a quantitative error scale for that model rather than simply assuming Gaussianity.

## 7. Current evidence

The project includes Lorenz, Rössler, forced Duffing dynamics, the circular restricted three-body problem, flexible-wing wind-tunnel data, bearing run-to-failure data, and shell-buckling data.

The strongest positive real-data result so far is the flexible-wing experiment. Before the large response at $28\,\mathrm{m/s}$, the full three-channel covariance-sensitivity increase from the previous parameter interval is about $2.10\times$, while the corresponding total-variance sensitivity increase is about $1.28\times$.

The bearing experiment gives the opposite result: RMS provides an earlier and more robust warning than the full covariance KL statistic.

The shell-buckling experiment also does not produce a validated covariance precursor.

These negative results are part of the test: the method should only outperform scalar indicators when genuinely multivariate precursor information is present.

## Main scientific question

> **Does loss of stability first appear as larger fluctuations, or as a change in how different measured variables fluctuate together?**

If it is mainly larger fluctuations, standard scalar indicators may already be sufficient.

If the relationships between variables change first, multivariate covariance sensitivity can reveal information that scalar early-warning signals cannot contain.

## Status

Active research.

The main empirical goal is prospective validation in physical systems where the mechanism of instability is known in advance to involve coupling between several modes or measured variables.
