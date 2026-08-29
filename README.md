# Early Signs of Instability

Many systems approach instability before any single measured signal becomes large.

Examples include an aircraft wing approaching flutter, a combustor approaching thermoacoustic oscillation, a power grid developing an oscillatory mode, or an ecosystem approaching a tipping point.

Most early-warning methods monitor one quantity at a time, such as variance, RMS, the largest covariance eigenvalue, or lag-1 autocorrelation.

This project asks:

> **Can instability first appear in how several measured variables fluctuate together, before any one scalar indicator gives a strong warning?**

The project focuses on the **covariance contribution** to local KL/Fisher sensitivity. Mean shifts can be analyzed separately.

## 1. What is measured?

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

The entries have a direct meaning:

- $\Sigma_{jj}$ is the variance of sensor $j$;
- $\Sigma_{jk}>0$ means sensors $j$ and $k$ tend to move above and below their means together;
- $\Sigma_{jk}<0$ means they tend to move in opposite directions;
- $\Sigma_{jk}\approx0$ means there is little linear co-fluctuation between them.

So $\Sigma$ contains both the variance of each sensor and the pairwise covariances between sensors.

## 2. How is covariance sensitivity computed?

Repeat the covariance calculation at several nearby values of the physical control parameter.

With measurements at $\theta-h$, $\theta$, and $\theta+h$,

```math
\Sigma'(\theta)
\approx
\frac{
\Sigma(\theta+h)-\Sigma(\theta-h)
}{
2h
}
```

Here $h$ is a step in the **scalar control parameter**, not in sensor space. For example, if $\theta$ is airspeed and measurements are taken at 24, 26, and 28 m/s, then $h=2$ m/s at the middle point.

This derivative estimates how every variance and covariance changes per unit change in $\theta$.

A raw derivative is not enough because sensors can have different units and variance levels. We therefore normalize by the current covariance.

If

```math
\Sigma=Q\Lambda Q^T
```

then

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

and

```math
\kappa(\theta)
=
\frac{1}{2}
\sum_{j,k}A_{jk}(\theta)^2
```

This is the covariance contribution to local Fisher information.

For nearby parameter values,

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

when only covariance change is considered.

So a large $\kappa$ means:

> **A small change in the physical control parameter produces a large relative change in the measured variances and cross-covariances.**

## 3. Two important scalar baselines

The full covariance statistic should not be compared only with the variance of one sensor. Two stronger baselines are the **trace** and the **largest covariance eigenvalue**.

### Total variance: trace

Define

```math
T(\theta)
=
\mathrm{tr}\,\Sigma(\theta)
=
\sum_j\Sigma_{jj}(\theta)
```

This is the sum of all sensor variances.

Its derivative is

```math
T'(\theta)
=
\frac{dT}{d\theta}
=
\mathrm{tr}\,\Sigma'(\theta)
=
\sum_j
\frac{d\Sigma_{jj}}{d\theta}
```

Numerically, it can also be estimated directly by

```math
T'(\theta)
\approx
\frac{
T(\theta+h)-T(\theta-h)
}{
2h
}
```

The corresponding relative sensitivity is

```math
\kappa_{\mathrm{trace}}(\theta)
=
\frac{1}{2}
\left(
\frac{T'(\theta)}{T(\theta)}
\right)^2
```

This asks:

> **Is the total amount of fluctuation increasing rapidly?**

### Largest covariance eigenvalue

Let $\lambda_1(\theta)$ be the largest eigenvalue of $\Sigma(\theta)$.

Its relative sensitivity is

```math
\kappa_{\lambda_1}(\theta)
=
\frac{1}{2}
\left(
\frac{\lambda_1'(\theta)}{\lambda_1(\theta)}
\right)^2
```

If the largest eigenvalue is simple and $v_1$ is its unit eigenvector, then

```math
\lambda_1'(\theta)
=
v_1^T
\Sigma'(\theta)
v_1
```

This asks:

> **Is the dominant fluctuation mode changing rapidly?**

The three quantities therefore answer different questions:

- **trace:** is total variance changing?
- **largest eigenvalue:** is the dominant mode changing?
- **full $\kappa$:** are any normalized variances, covariances, or mode directions changing?

## 4. Worked Lorenz example

Consider the Lorenz system

```math
\dot x=10(y-x)
```

```math
\dot y=x(\rho-z)-y
```

```math
\dot z=xy-\frac{8}{3}z
```

with control parameter $\rho$.

For this worked example:

- initial condition: $(1,1,1)$;
- $\rho=24.0,\ 24.5,\ 25.0$;
- first 20 time units discarded;
- next 40 time units used;
- sampling interval: $0.01$.

At $\rho=24.5$, the sample covariance is approximately

```math
\Sigma
=
\begin{bmatrix}
52.77 & 52.75 & 2.98 \\
52.75 & 66.67 & 2.58 \\
2.98 & 2.58 & 62.67
\end{bmatrix}
```

Using $h=0.5$,

```math
\Sigma'(24.5)
\approx
\frac{
\Sigma(25.0)-\Sigma(24.0)
}{
1.0
}
```

which gives

```math
\Sigma'
\approx
\begin{bmatrix}
13.37 & 12.95 & 8.98 \\
12.95 & 16.69 & 3.61 \\
8.98 & 3.61 & 17.01
\end{bmatrix}
```

After normalization,

```math
A
=
\Sigma^{-1/2}
\Sigma'
\Sigma^{-1/2}
\approx
\begin{bmatrix}
0.269 & -0.026 & 0.217 \\
-0.026 & 0.271 & -0.070 \\
0.217 & -0.070 & 0.264
\end{bmatrix}
```

Therefore

```math
\kappa
=
\frac{1}{2}
\sum_{j,k}A_{jk}^2
\approx
0.1606
```

### Trace calculation

For the same covariance matrix,

```math
T
=
52.77+66.67+62.67
\approx
182.11
```

and

```math
T'
=
13.37+16.69+17.01
\approx
47.08
```

because $T'=\mathrm{tr}\,\Sigma'$.

Therefore

```math
\kappa_{\mathrm{trace}}
=
\frac{1}{2}
\left(
\frac{47.08}{182.11}
\right)^2
\approx
0.0334
```

### Largest-eigenvalue calculation

For the same $\Sigma$,

```math
\lambda_1
\approx
113.23
```

and, using its unit eigenvector $v_1$,

```math
\lambda_1'
=
v_1^T\Sigma'v_1
\approx
29.34
```

so

```math
\kappa_{\lambda_1}
=
\frac{1}{2}
\left(
\frac{29.34}{113.23}
\right)^2
\approx
0.0336
```

For this particular finite Lorenz trajectory,

```math
\kappa
\approx
0.1606,
\qquad
\kappa_{\mathrm{trace}}
\approx
0.0334,
\qquad
\kappa_{\lambda_1}
\approx
0.0336
```

The full covariance sensitivity is therefore about $4.8$ times either scalar sensitivity in this example.

This calculation is included to show **exactly how the statistic is computed**. Lorenz is chaotic, so numerical values depend on trajectory length, initial condition, sampling, and parameter spacing. These numbers are not presented as evidence that the full statistic is always superior.

## 5. What can full covariance detect that the baselines miss?

Suppose several fluctuation modes reorganize while total variance stays almost constant.

Then

```math
T'(\theta)
\approx
0
```

even though the covariance matrix can change substantially.

The same can happen to the largest eigenvalue. The eigenvalues can even stay fixed while the covariance eigenvectors rotate.

In such a case,

```math
T'(\theta)=0
```

and

```math
\lambda_1'(\theta)=0
```

while the full covariance sensitivity can still satisfy

```math
\kappa(\theta)>0
```

This is the clearest case in which a multivariate warning contains information unavailable to trace or largest-eigenvalue monitoring.

The opposite case is equally important. If one mode dominates the approach to instability, $\kappa_{\lambda_1}$ can contain nearly all of the useful covariance information. The method does **not** claim an advantage in that regime.

## 6. Practical implications

### Aeroelastic flutter

Flutter can result from coupling between bending and twisting modes.

The method tests whether covariances between strain and acceleration channels change before vibration amplitude or the dominant covariance eigenvalue gives a strong warning.

### Thermoacoustic instability

Combustion instability is driven by interaction between pressure, heat release, and flow.

The method tests whether their cross-covariances reorganize before pressure RMS becomes large.

### Power grids

An oscillatory grid instability can involve coordinated motion across many buses.

The method uses voltage, phase, and frequency measurements jointly and can detect changes in the spatial fluctuation pattern.

### Ecology

An ecosystem may lose resilience through changing relationships between species.

The method tests whether cross-species covariances change before any single population or total-variance measure gives a strong warning.

## 7. Why use a Gaussian approximation?

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

## 8. Current evidence

The project includes Lorenz, Rössler, forced Duffing dynamics, the circular restricted three-body problem, flexible-wing wind-tunnel data, bearing run-to-failure data, and shell-buckling data.

The strongest positive real-data result so far is the flexible-wing experiment. Before the large response at $28\,\mathrm{m/s}$, the full three-channel covariance-sensitivity increase from the previous parameter interval is about $2.10\times$, while the corresponding total-variance sensitivity increase is about $1.28\times$.

The bearing experiment gives the opposite result: RMS provides an earlier and more robust warning than the full covariance KL statistic.

The shell-buckling experiment also does not produce a validated covariance precursor.

These negative results are part of the test: the method should only outperform scalar indicators when genuinely multivariate precursor information is present.

## Main scientific question

> **Does loss of stability first appear as larger fluctuations, growth of one dominant mode, or a reorganization of how several variables fluctuate together?**

If it is mainly larger fluctuations, trace or RMS may already be sufficient.

If one dominant mode grows, the largest covariance eigenvalue may be sufficient.

If several modes or their relationships reorganize first, full covariance sensitivity can contain information that those scalar indicators cannot retain.

## Status

Active research.

The main empirical goal is prospective validation in physical systems where the mechanism of instability is known in advance to involve coupling between several modes or measured variables.
