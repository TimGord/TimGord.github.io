---
draft: true 
date: 2024-10-20
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

# Don't forget to add the <!-- more --> separator!!!
---

# Mortality experience analysis

Over the years I have witnessed confusions and errors relating to mortality experience analysis that I think were avoidable, to say nothing about my own paranoia over making mistakes.

So I have set out on this page a conceptual framework that I have found helpful together with the insights I think it provides.

<!-- more -->

This is not new. The AIC-related maths is set out in [Model Selection and Multimodel Inference: A practical information-theoretic approach (2nd ed.)](https://link.springer.com/book/10.1007/b97636) by Kenneth Burnham and David Anderson[^BurnhamAndersonCh7]. And I've previously presented this publicly, [in particular in relation to weighted mortality analysis](https://www.actuaries.org.uk/system/files/documents/pdf/tim-gordon.pdf). 

[^BurnhamAndersonCh7]: ISBN-13: 9780387953649. At time of writing Chapter 7, which contains relevant maths is [free on the Springer website](https://link.springer.com/chapter/10.1007/978-0-387-22456-5_7). See also Multimodel Inference: Understanding AIC and BIC in Model Selection ([PDF here](http://www.sortie-nd.org/lme/Statistical%20Papers/Burnham_and_Anderson_2004_Multimodel_Inference.pdf), doi:[10.1177/0049124104268644](https://doi.org/10.1177%2F0049124104268644).

But Stephen Richards' and Angus Macdonald's excellent [paper on mortality models](https://www.longevitas.co.uk/sites/default/files/2024-08/On_contemporary_mortality_models_for_actuarial_use.pdf) -- on which my comments can be found [here](/2024-10/on-contemporary-mortality-models-for-actuarial-use/) -- has prompted me to pull various strands together in one place for easy reference.

## Weighting

===================== BEGIN TODO ===================== 

Macdonald and Richards [2024,
Section 4.3] show how both left truncation and right censoring can be handled by setting an exposure
indicator function, Y (x), to zero to ‘switch off’ the contribution of exposure time and observed deaths
to estimation. Y (x) is a multiplier of µx, and to exclude the contribution of covid-affected experience,
one could also set Y (x) = 0 for the period of any mortality shocks.

Relevance/importance

Financial impact

Talk about this in another post

Weights does not just mean amounts

Experience data declines in relevance

Volatility in CMI Model arising directly from data falling out of the 40 year calibration period -- crazy that events 40 years ago affect current best estimate

===================== END TODO ===================== 

## Preliminaries

1. All **time intervals** are open-closed, i.e. $(\nu,\tau]$ where $\nu\le\tau$. An the interval contains $t$ iff $\nu\le t\lt\tau$.

1. An **'E2R'** -- the core mortality-experience data item -- is a triple $(\nu,\tau,\delta)$ where

    - $(\nu,\tau]$ is the time interval of the individual's **exposure**, and
    - $\delta$ is $1$ if the individual **died at the end of the exposure** and $0$ otherwise.

    If the exposure is empty, i.e. $\nu=\tau$, then $\delta$ must be $0$[^EmptyE2R].

[^EmptyE2R]:  An empty E2R for interval $[t,t)$ contains no information. In particular is does *not* imply that the individual was still alive at time $t$.

1. An **experience dataset** $\mathscr{E}$ relating to the overall time interval $(N,T]$ comprises pairs $(i,\varepsilon)$ where

    - $i$ is a vector of the individual's time-invariant data items, e.g. birth date, sex/gender, etc[^NoCheating], and
    - $\varepsilon$ is the the individual's E2R, where $(\nu_\varepsilon,\tau_\varepsilon] \subseteq (N,T]$.

[^NoCheating]: The only thing $i$ can't contain is whether the individual lived or died.

    If there are multiple entries for the same individual then the exposures of those entries cannot intersect.

1. A **factor** is a function $f(i,t)$ of individual member data and time, that is [left continuous](https://en.wikipedia.org/wiki/Continuous_function#Directional_Continuity) in time, i.e.

    $$f(i,t) = \underset{s \uparrow t}\lim \, f(i,s)$$

1. A **mortality model** $\mu(i,t)$ gives the probability of an individual $i$ dies in an infinitesimal time interval $(t,t+\text{d}t]$ given they were alive at the start of the interval, i.e.

    $$\mathbb{P}\big(i\text{ dies in } (t,t+\text{d}t] \,\big|\;i\text{ alive at }t\big)=\mu(i,t)\text{d}t$$

Now we are ready to proceed with the useful stuff.


## Framework

### Defining actual and expected deaths

Define analogues of integration/summation of an arbitrary factor $f(i,t)$ over an experience dataset for actual deaths ($\text{A}$) and 'expected' deaths ($\text{E}$) as:

$$\begin{aligned}
\text{A}_\mathscr{E}f&=\sum_{(i,\varepsilon)\in \mathscr{E}}\delta_\varepsilon f(i,\tau_\varepsilon)
\\[1em]
\text{E}_\mathscr{E}f&=\sum_{(i,\varepsilon)\in \mathscr{E}}\int_{\nu_\varepsilon}^{\tau_\varepsilon}\mu(i,t)f(i,t)\,\text{d}t
\end{aligned}$$

> We'll drop the subscript $\mathscr{E}$ when we are working with just one experience dataset.

$\text{A}$ and $\text{E}$ are linear operators[^AELinear]. *Both* $\text{A}f$ and $\text{E}f$ are random variables[^EAbuse], although we suggest that, in typical scenarios, the variation of $\text{E}f$ is much less than the variation of $\text{A}f$.

*If $\mu$ is the true mortality* then, *for any factor $f$*, the following are mathematical identities:

[^AELinear]: $\text{A}f$ and $\text{E}f$ being [linear operators](https://en.wikipedia.org/wiki/Operator_(mathematics)#Linear_operators) means that (a)&#x00A0;they are straighforward to manipulate and (b)&#x00A0;typically do not need their operands enclosed in brackets.

[^EAbuse]: Describing $\text{E}f$ as 'expected' deaths is an abuse of terminology (albeit a standard one).

$$\begin{aligned}
\mathbb{E}\big(\text{A}f-\text{E}f\big)&=0
\\[0.5em]
\text{Var}\big(\text{A}f-\text{E}f\big)&=\mathbb{E}\big(\text{E}f^2\big)
\end{aligned}$$

!!! info "Insight 1 of ##"

    These are the canonical definitions of A and E. Confusion and spurious complexity can arise over the interpretation of actual and expected deaths when using other definitions.

!!! info "Insight 2 of ##"

    If a model is true then we *expect* actual and 'expected' deaths to be equal (a)&#x00A0;*over any subset* of the experience data and (b)&#x00A0;*for any weight*.

### A over E

The classic diagnostic when reviewing mortality experience is to compare [actual vs expected (weighted) deaths](https://www.actuarialstandardsboard.org/glossary/actual-to-expected-ae-ratio/) on a prior mortality model, where the weight[^Weights] $w(i,t)$ is a non-negative factor.

[^Weights]: Typical weights are 'lives', i.e. 1 (unweighted), and 'amounts', which means benefit amount revalued (a)&#x00A0;for consistency between exposures and deaths and (b)&#x00A0;comparability over time.

The above variance result suggests the normal approximation

$$\text{A}w / \text{E}w \sim \text{N}\Big(1,\text{E}w^2 / (\text{E}w)^2\Big)$$

in which case the diagnostic it to compare A/E[^LogAOverE] to 100% and assess the difference relative to $\pm\sqrt{\text{E}w^2/(\text{E}w)^2}$.

[^LogAOverE]:

    Or, in log&#x202F;*μ* space,

    $$\log\!\big(\text{A}w / \text{E}w\big) \sim \text{N}\big(1,\text{E}w^2 / (\text{A}w\,\text{E}w)\big)$$

    In the cases where they differ materially, the standard A/E will be materially different from 100% in which case the diagnostic will be failed under either approach.

!!! info "Insight 3 of ##"

    A and E as defined here lead directly to a well-defined and parsimonious measure of weighted A/E itself *and* an estimate of its uncertainty.

!!! info "Insight 4 of ##"

    In the lives-weighted case, $\text{E}w^2 = \text{E}w$ and hence the variance estimate is $1 / \text{E}w$. There are two things to avoid:
    
    1. The lives-weighted variance estimate should not be used to estimate the variance of a weighted A/E because it will always understate it.

    1. If this assumption is built into an implementation then that implementation will struggle when weighted results are required.

!!! info "Insight 5 of ##"

    Providers of grouped A and E data often provide it weighted by both lives and amounts. What they almost always overlook -- [including the CMI (paywalled)](https://www.actuaries.org.uk/documents/cmi-wp190-dataset) -- is that the amounts-weighted data should also include E weighted by amount *squared*.

### Measures and partitions

$\text{A}$ and $\text{E}$ are both [*measures*](https://en.wikipedia.org/wiki/Measure_(mathematics)) over experience data[^ExpDims]. This means that a linear combination of $\text{A}$ and $\text{E}$ gives the same result when summed over a partitioned experience dataset *regardless of how it is partitioned*.

[^ExpDims]: There are two axes for assessing whether experience data intersect, *identity* and *time*. Specifically, the data $(i_1,\varepsilon_1)$ and $(i_2,\varepsilon_2)$ intersect iff both

    - $i_1$ and $i_2$ relate to the same individual (*identity*), and

    - the exposure time intervals for $\varepsilon_1$ and $\varepsilon_2$ intersect (*time*).

    Time intersection can be determined direct from the data. Identity intersection requires either $i$ to contain a [UID](https://en.wikipedia.org/wiki/Unique_identifier) or external knowledge.

!!! info "Insight 6 of ##"

    If you use $\text{A}$ and $\text{E}$ you can fearlessly[^ApologiesRust] split or combine experience data in any way *provided the data does not intersect with itself*.

    In particular, there is *no need*

    - *to track individuals across experience datasets for different time periods*[^Tracking], or

    - *for the time periods to be contiguous*[^Contiguity].

[^ApologiesRust]: With apologies to [Rust](https://doc.rust-lang.org/book/ch16-00-concurrency.html).

[^Tracking]: Tracking individuals across experience datasets for different time periods can make sense as a data check or simply trying to understand the data.

[^Contiguity]: Gaps in data are equivalent to setting the weight to zero for the non-included time periods.

!!! info "Insight 7 of ##"

    If you want performant analysis and model fitting for experience data then consider (a)&#x00A0;using operations based on $\text{A}$ and $\text{E}$, and (b)&#x00A0;running the code implementing those operations in parallel on partitions of the data.

### Log-likelihood and model fitting

We can define the (weighted[^WeightedL]) log-likelihood of an experience dataset for a specified mortality model $\mu$ directly in terms of the $\text{A}$ and $\text{E}$ operators as

[^WeightedL]:  Weighting log-likelihoods, i.e. placing more statistical weight on some data than others, is seen as controversial in some quarters. If you are uncomfortable with the notion of weighting then read the rest of this notes assuming that only lives weights are used, i.e. the weight is only ever $0$ or $1$. I'd suggest that -- even if you object to weighting log-likelihood e.g. by benefit amount -- there are cases where having the ability to weight log-likelihood is helpful. I suspect this is a subject for another post. 

$$L=\text{A}w\log\mu-\text{E}w$$

$L$ is a linear combination of $A$ and $E$, and so it too can be summed over a partitioned experience dataset *regardless of how it is partitioned*.

In order to make progress, assume that the mortality model is a [proportional hazards model](https://en.wikipedia.org/wiki/Proportional_hazards_model), i.e.

$$\mu=\mu^\text{ref}\exp\!\big(\beta^\text{T}X\big)$$

where $X$ is a vector[^LinAlg] of factors ('covariates') and $\mu^\text{ref}$ is a reference mortality model, both of which are independent of $\beta$.

[^LinAlg]: I use the following notation for linear algebraic:

    - $^\text{T}$ means vector or matrix [transpose](https://en.wikipedia.org/wiki/Transpose).
    - $\beta^\text{T}X$ means the ['dot' or 'scalar' product](https://en.wikipedia.org/wiki/Dot_product) of vectors $\beta$ and $X$, i.e. $\beta^\text{T}X=\sum_j \beta_j X_j$.
    - Matrices are shown in bold caps, e.g. $\mathbf{A}$.

!!! info "Insight 8 of ##"

    Proportional hazards models are not only sufficiently powerful to cope with almost all practical mortality modelling problems but, critically, they are tractable (not least as as evidenced by this note). They are also arguably *the* canonical mortality model[^CanonPH].

[^CanonPH]:

    I suggest that the proportional hazards model is *the* canonical mortality model in the sense that differentiating the log-likelihood results in 

    $$\text{A}w\frac{1}{\mu}\frac{\partial \mu}{\partial \beta} = \text{E}w\frac{1}{\mu}\frac{\partial \mu}{\partial \beta}$$

    and if we define the common operand of both $\text{A}$ and $\text{E}$ as

    $$X=\frac{1}{\mu}\frac{\partial \mu}{\partial \beta}$$

    it is natural to require that $X$ be independent of $\beta$, in which case the proportional hazards model falls out directly as the solution, i.e.

    $$\mu=\mu^\text{ref}\exp\!\big(\beta^\text{T}X\big)$$

We can estimate $\beta$ by [maximising the log-likelihood](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) with respect to $\beta$, which is equivalent to the (vector) condition $\partial L / \partial \beta=0$, which, for the proportional hazards model is simply

$$\text{A}wX = \text{E}wX$$

!!! info "Insight 9 of ##"

    If any single covariate in $X$ is a constant (i.e. independent of individuals and time) then fitting this model guarantees $Aw/Ew=1$ for the experience data to which the model has been fitted.
    
    In practice, age-dependence means that covariates *do* depend on time, but the dependence is sufficiently weak to that fitted models still result in $Aw/Ew\approx1$.

### Parameter uncertainty

> I'll use $'$ to denote $\partial / \partial\beta$ from here on.

We can estimate the uncertainty of the best estimate vector of parameters $\hat\beta$ using

$$\text{Var}\big(\hat\beta\big)\approx\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}$$

where the square matrices $\mathbf{I}=-L''(\hat\beta)$ and $\mathbf{J}$ estimates $\mathbb{E}\,L'(\beta)^\text{T}L'(\beta)$.

For a lives-weighting, $\mathbf{I}\approx\mathbf{J}$ and hence $\text{Var}\big(\hat\beta\big)\approx\mathbf{I}^{-1}$.

For the proportional hazards model, we have the elegant result

$$\begin{aligned}
\mathbf{I}&=\text{E}wXX^\text{T}
\\[1em]
\mathbf{J}&=\text{E}w^2XX^\text{T}
\end{aligned}$$

!!! info "Insight 10 of ##"

    This result extends the conventional AIC to the weighted log-likelihood case.

### Generalised AIC

The generalisation of the [Akaike information criterion](https://en.wikipedia.org/wiki/Akaike_information_criterion) (AIC) under this framework is equivalent[^AICDef] to using the following penalised log-likelihood:

[^AICDef]:

    The AIC is conventionally defined as $-2$ what I have called penalised log-likelihood for consistency with the normal distribution and [deviance](https://en.wikipedia.org/wiki/Deviance_(statistics)), i.e.

    $$\text{AIC}=-2L_\text{P}$$

    I think this is unhelpful because if obscures the link between the AIC and (a)&#x00A0;log-likelihood as a measure of information and (b)&#x00A0;the justification of the AIC as an *unbiased* estimator of the [Kullback-Leibler divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) from the (notional) true underlying model.

$$L_\text{P}=L(\hat\beta)-\text{tr}(\mathbf{I}^{-1}\mathbf{J})$$

For lives-weighted modelling, this collapses to the conventional form

$$L_\text{P}=L(\hat\beta)-\dim(\beta)$$

!!! info "Insight 11 of ##"

    This result extends the conventional AIC to the weighted log-likelihood case.

## Overdispersion

In the real world, mortality experience data typically exhibits greater variance than implied by the [Poisson distribution](https://en.wikipedia.org/wiki/Poisson_distribution) that the above approach assumes. This is commonly known as [overdispersion](https://en.wikipedia.org/wiki/Overdispersion#Poisson). This is does not usually affect the estimates themselves, but it does affect their estimated variance and leading to the AIC potentially overfitting models.

!!! info "Insight 12 of ##"

    Not allowing for overdispersion will, in practice, result in over-fitted mortality models.

The simplest approach is simply to divide the log-likelihood by an estimate of the overdispersion, $\Omega$, in which case it becomes a [quasi-log-likelihood](https://en.wikipedia.org/wiki/Quasi-likelihood) and the equations above are adjusted as follows:

|A|B|
|:--:|:--:|
|$E=mc$|$E=mc$|


**TODO!!!!!!**
