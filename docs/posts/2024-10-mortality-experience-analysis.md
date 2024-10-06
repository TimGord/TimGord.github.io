---
draft: true 
date: 2024-10-06
authors:
  - timgord
categories:
  #- Coding
  #- Forecasting
  - Maths/stats
  - Mortality/longevity
  #- Presentation

#tags:
#  - Coding
#  - Mortality
#  - Forecasting
#  - Standards

# For reasons I don't understand, the local and server slugification produce
# different results when there are dashes in the item title.
#slug: add-custom-slug-here
---

# Mortality experience analysis

Over the years I have witnessed confusions and errors relating to mortality experience analysis, in both theory and implementation that I think were avoidable.

So I have set out below a conceptual framework that I have personally found helpful myself.

<!-- more -->

This is not new -- I've spoken about it before [e.g. in  2011](https://www.actuaries.org.uk/system/files/documents/pdf/tim-gordon.pdf).


## Definitions:

- Linear algebra, matrices and vectors, using $^\text{T}$ for transpose and $'$ to indicate (possibly) vector differentiation.

## Core concepts

### Time

- Exposed-to-risk
- Individual data

## 1. Exposed-to-risk (E2R)

An E2R, $\varepsilon$, is a tuple $(\nu,\tau,\delta)$ where
   - $\nu$ is the start of the exposure period,
  - $\tau$ is the end of the exposure period, and
  - $\delta$ is an indicator equal to 1 if the individual died at the end of the exposure period and 0 otherwise.

> I think the time interval is technically $[\nu,\tau)$.

Minimum requirements for a valid E2R in isolation are
- $\nu\le\tau$
- if $\nu=\tau$ then $\delta=0$.

Or simply require that E2Rs are non-empty, i.e. $\nu\lt\tau$.

There are additional consistency requirements for E2Rs for the same individual
- the time ranges must be contiguous, i.e. partition one time interval, and
- $\delta$ can be $1$ only in the final E2R.

## 2. Experience data

An experience dataset is the combination of data specified to each individual (which we require to be independent of time) and E2Rs. More formally, $\mathscr{E}$ is an experience dataset of tuples $(i,\varepsilon)$ where 
  - $i$ is time-invariant individual data, and
  - $\varepsilon$ is exposed-to-risk for that individual.

We require that exposure periods do not overlap for the same individual.

> In practice, de-duplication is not perfect and experience data *do* typically contain some overlaps but  
> - these are usually well within noise tolerance and, 
> - when weighted by a value that is summed on de-duplication (e.g. benefit amount) case, affects only the estimated variance rather estimation of the mean itself.

## 3. A and E operators

For a given (force of) mortality $\mu_{it}$, we define the following operators on a weight function $w_{it}$

$$\begin{aligned}
\text{E}w&=\sum_{(i,\varepsilon)\in \mathscr{E}}\int_{\nu_\varepsilon}^{\tau_\varepsilon}\mu_{it}w_{it}\,\text{d}t\\[20pt]
\text{A}w&=\sum_{(i,\varepsilon)\in \mathscr{E}}\delta_\varepsilon w_{i\tau_\varepsilon}\tag{1}
\end{aligned}$$

where $w_{it}$ and $\mu_{it}$ are both functions of individual data, $i$, and time, $t$, with $\mu_{it}>0$.

We call $\text{A}$ and $\text{E}$ 'actual' and 'expected', but this is a (we think reasonable) abuse of terminology because they are *both* random variables.

> $\text{A}$ and $\text{E}$ are both *measures* on experience data, which means we can sum them over any partition of experience data that does not intersect by time and individual. In other words, it does not affect the answer if we break data down into  subsets with individuals in common provided their exposure periods in the subsets do not overlap. (We routinely rely on this property, including for running our C# code in parallel.)

**If $\mu$ is the true underlying mortality** then we have

$$\begin{aligned}
\mathbb{E}\left(\text{A}w-\text{E}w\right)&=0\\[5pt]
\text{Var}\left(\text{A}w-\text{E}w\right)&=\Omega\,\mathbb{E}\,\text{E}w^2\tag{2}
\end{aligned}$$

where $\mathbb{E}$ is the (true) expectation operator and $\Omega$ is [overdispersion](https://en.wikipedia.org/wiki/Overdispersion), i.e. the ratio of observed variance to variance predicted by the Poisson distribution.

> Typical *a priori* values for $\Omega$ in a mortality context are between $1.5$ and $3$ -- $2$ is reasonable default.

We can use the above to derive confidence intervals for A/E by making the simplifying assumptions that
- $\text{E}w$ and $\text{E}w^2$ are known constants and
- $\text{A}w$ is normally distributed, 

leading to
$$\frac{\text{A}w}{\text{E}w}\sim \text{N}\left(1\,,\,\frac{\Omega\,\text{E}w^2}{(\text{E}w)^2}\right)$$

This approach is a useful diagnostic, but we can improve on this -- see below.

## 4. Log likelihood and proportional hazards

We can define the *weighted* log-likelihood in terms of the $\text{A}$ and $\text{E}$ operators as 

$$L=\Omega^{-1}\big(\text{A}w\log\mu-\text{E}w\big)$$

where $\Omega$ is overdispersion.

> Dividing the Poisson log-likelihood through by $\Omega$ give a [quasi log likelihood](https://en.wikipedia.org/wiki/Quasi-likelihood).

If our mortality model has a parameter vector $\beta$ then we can estimate it as

$$\hat\beta=\underset{\beta}{\arg\max}\,L(\beta)$$

We work with proportional hazards models of the form

$$\mu_{it}=\mu^\text{Ref}_{it}\exp(\beta^\top X_{it})$$

where 
- $X_{it}$ is a *vector* of functions of immutable individual data, $i$, and time, $t$, and
- $\beta$ is a vector of values to be fitted.

$X_{it}$ and $\beta$ are generally known as the 'covariates' and 'covariate weights' respectively.

From this point, we will assume the mortality model being calibrated is a proportional hazards model in the form above.

>It is useful to note that maximising the weighted log-likelihood for a proportional hazards model is equivalent to satisfying the vector condition
>
>$$\text{A}wX=\text{E}wX$$
>
>In practice, this means that if our model includes a flat scaling term then it is mathematically true that we will have $\text{A}w=\text{E}w$. We typically use covariates comprising indicators multiplied by a function declining with age, but even for these, we expect $\text{A}w\approx\text{E}w$.

Our *estimate* of the covariance of $\beta$ is (allowing for overdispersion)

$$\text{Var}(\beta)\approx \Omega  I^{-1}JI^{-1}$$

where

$$\begin{aligned}
I&=\Omega^{-1}\,\text{E}wXX^\text{T}=-L''(\hat\beta)\\[5pt]
J&=\Omega^{-2}\,\text{E}w^2XX^\text{T}\approx \mathbb{E}\left\{L'(\beta)(L'(\beta))^\text{T}\right\}
\end{aligned}$$

> **Motivation**
> 
> Expand the first differential of the weighted log-likelihood around $\hat\beta$:
> $$L'(\beta)\approx L'(\hat\beta)+L''(\hat\beta)\cdot(\beta-\hat\beta)$$
> 
> By definition, $L'(\hat\beta)=0$ and so we can rearrange this as
> $$\beta-\hat\beta=-I^{-1}L'(\beta)$$
> 
> Squaring and taking expectations, and then including overdispersion, results in
> 
> $$\text{Var}(\beta)=\mathbb{E}(\beta-\hat\beta)^2\approx \Omega\,I^{-1}JI^{-1}$$
> 
> where we have used the result from $(2)$ above.

Finally, the *penalised* log likelihood equivalent to a weighted AIC/TIC is

$$L_\text{P}=L-\text{tr}(JI^{-1})$$

If the weight is constrained to 0 or 1, i.e. $w\in\{0,1\}$, then this collapses to the conventional AIC, which is the number of parameters.

The underlying rationale for the penalty is based on an unbiased estimate of the weighted Kullback-Leibler divergence for the fitted model compared with a notional true model. *Provided the model being calibrated is itself a reasonable model* then this estimate is equivalent to the following penalised log-likelihood: 
>
>$$L+\text{tr}\left(L''\text{Var}(\hat\beta)\right)
\approx L-\text{tr}\left(II^{-1}JI^{-1}\right)
=L-\text{tr}(JI^{-1})$$

## 5. An improved diagnostic

It is common to refer to $A/E$ ('A over E') and so the first port of call is typically to review precisely that, i.e. $\text{A}w/\text{E}w$. This does have a number of (related) drawbacks:

1. It contradicts our core modelling approach, which is to uses $\log\mu$ as the basic metric.
1. It seems anti-intuitive that an actual of 50% of expected should look twice as good as an actual of 200% of expected.

An alternative is to estimate variation in $\log\mu$ space using the log-likelihood methodology above applied to the simplest possible single variable proportional hazards model for variation in mortality relative to our prior model $\mu^\text{P}$, i.e.

$$\mu^\text{V}=e^\beta \mu^\text{P}$$

The log-likelihood is 

$$\begin{aligned}
L(\beta)&=\text{A}w\log\mu^\text{V}-\text{E}^\text{V}w\\[0.5em]
&=\text{A}w\left(\beta+\log\mu^\text{P}\right)-e^\beta\text{E}^\text{P}w
\end{aligned}$$

Using the results from the previous section, the estimator for $\beta$ and its variance are

$$\begin{aligned}
\hat\beta&=\log\left(\frac{\text{A}w}{\text{E}^\text{P}w}\right)\\[1.5em]
\text{Var}(\hat\beta)&\approx\frac{\text{E}^\text{V}w^2}{(\text{E}^\text{V}w)^2}=\frac{e^{\hat\beta}\text{E}^\text{P}w^2}{(e^{\hat\beta}\text{E}^\text{P}w)^2}=\frac{\text{E}^\text{P}w^2}{\left(\text{A}w\right)\left(\text{E}^\text{P}w\right)}\\
\end{aligned}$$

This suggests that, instead of $\text{A}w/\text{E}w$, we use the following as our diagnostic

$$\log\left(\frac{\text{A}w}{\text{E}w}\right)\sim\text{N}\left(0,\;\frac{\text{E}w^2}{\left(\text{A}w\right)\left(\text{E}w\right)}\right)$$

> See the separate note on AE residuals.


