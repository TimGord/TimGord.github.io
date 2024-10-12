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

So I have set out on this page a coherent conceptual framework that I have found helpful, together with the insights I think it provides.

<!-- more -->

This is not new. The AIC-related maths is set out in widely influential [Model Selection and Multimodel Inference: A practical information-theoretic approach (2nd ed.)](https://link.springer.com/book/10.1007/b97636) by Kenneth Burnham and David Anderson[^BurnhamAnderson]. And I've [presented on this publicly for over a decade](https://www.actuaries.org.uk/system/files/documents/pdf/tim-gordon.pdf). 

[^BurnhamAnderson]:

    [Model Selection and Multimodel Inference: A practical information-theoretic approach (2nd ed.)](https://link.springer.com/book/10.1007/b97636), ISBN-13: 9780387953649.
    
    At time of writing, Chapter 7, which contains the relevant maths is [free on the Springer website](https://link.springer.com/chapter/10.1007/978-0-387-22456-5_7).
    
    See also Multimodel Inference: Understanding AIC and BIC in Model Selection ([PDF here](http://www.sortie-nd.org/lme/Statistical%20Papers/Burnham_and_Anderson_2004_Multimodel_Inference.pdf), doi:[10.1177/0049124104268644](https://doi.org/10.1177%2F0049124104268644)).

But Stephen Richards' and Angus Macdonald's excellent [paper on mortality models](https://www.longevitas.co.uk/sites/default/files/2024-08/On_contemporary_mortality_models_for_actuarial_use.pdf) -- on which my comments can be found [here](/2024-10/on-contemporary-mortality-models-for-actuarial-use/) -- has prompted me to pull various strands together in one place for easy reference.

## Preliminaries

1. An **'E2R'** -- the core mortality-experience data item -- is a triple $(\nu,\tau,\delta)$ where

    - $[\nu,\tau)$ is the time interval of the individual's **exposure**, and
    - $\delta$ is the indicator $1$ if the individual **died** at $\tau$, i.e. immediately after the end of the exposure, and $0$ otherwise.

    We require $\nu\le\tau$. If the exposure is empty, i.e. $\nu=\tau$, then $\delta$ must be $0$ and the E2R is treated as containing no information. In particular, an empty E2R does *not* imply that an individual was still alive at $\nu$.

1. An **experience dataset** $\mathscr{E}$ relating to the overall time interval $[N,T)$ comprises pairs $(i,\varepsilon)$ where

    - $i$ is a vector of the individual's time-invariant data items, e.g. birth date, sex/gender, etc[^NoCheating], and
    - $\varepsilon$ is the the individual's E2R, where $[\nu_\varepsilon,\tau_\varepsilon) \subseteq [N,T)$.

    [^NoCheating]: The only things $i$ can't contain are the end time or death indicator of any associated E2R.

    If there are multiple entries for the same individual then the exposures of those entries cannot intersect.

1. A **factor** is a function $f(i,t)$ of individual member data and time, that is left continuous in time[^FactorSmoothness], i.e.

    $$f(i,t) = \underset{s \uparrow t}\lim \, f(i,s)$$

    [^FactorSmoothness]: Left continuity is a technical device for this exposition that allows us to represent subsets using factors that take the values $0$ or $1$. In a practical system, we'd want factors not only to be continuous but also 'smooth' (in the sense that they can be interpolated linearly over short time intervals), and we'd use a different means to select subsets -- see next note.

1. A **mortality model** $\mu(i,t)$ is a strictly positive continuous factor giving the probability of an individual $i$ dying in an infinitesimal time interval $(t,t+\text{d}t]$ given they were alive at the start of the interval, i.e.

    $$\mathbb{P}\big(i\text{ dies in } (t,t+\text{d}t] \,\big|\;i\text{ alive at }t\big)=\mu(i,t)\text{d}t$$

The above are the minimum requirements[^ExtendedFeatures] to proceed.

[^ExtendedFeatures]: In practical applications, we also need the following:

    - To select subsets of experience data *efficiently*, we need maps from individuals to closed-open, i.e. $[t,u)$ *time intervals* and to define the intersection between an E2R and a time interval.
     
        In theory, we could use factors returning 0 or 1 for this, but, as well as this being woefully inefficient, practical systems are likely to assume that factors are 'smooth' (so that they can be integrated numerically).

    - A mortality model would have an *end time* as a function of the individual's data, $\zeta_\mu(i)$, from which time the model asserts that that individual must be dead. In practice, these are always ages, e.g. $\zeta_\mu(i)=b_i+120\text{ years}$, where $b_i$ is the birth date of individual $i$. It is a fundamental model error (e.g. [NaN](https://en.wikipedia.org/wiki/NaN)) for any individual in an experience (or valuation) dataset to be alive at $\zeta_\mu(i)$ or later*. End times are required for mortality projections (e.g. in present value calculations), but experience analysis is usually restricted to ages well before model end ages and so we ignore them in this note. 

## Framework

### Defining actual and 'expected' deaths

Define analogues of integration/summation of an arbitrary factor $f(i,t)$ over an experience dataset for actual deaths ($\text{A}$) and 'expected' deaths ($\text{E}$) as:

$$\begin{aligned}
\text{A}_\mathscr{E}f&=\sum_{(i,\varepsilon)\in \mathscr{E}}\delta_\varepsilon f(i,\tau_\varepsilon)
\\[1em]
\text{E}_\mathscr{E}f&=\sum_{(i,\varepsilon)\in \mathscr{E}}\int_{\nu_\varepsilon}^{\tau_\varepsilon}\!\mu(i,t)f(i,t)\,\text{d}t
\end{aligned}$$

> We'll drop the subscript $\mathscr{E}$ when we are working with just one experience dataset.

$\text{A}$ and $\text{E}$ are linear operators[^AELinear] on factors. It is also the case that both $\text{A}f$ and $\text{E}f$ are random variables[^EAbuse], although we suggest that, in typical scenarios, the variation of $\text{E}f$ is much less than the variation of $\text{A}f$.

[^AELinear]: $\text{A}f$ and $\text{E}f$ being [linear operators](https://en.wikipedia.org/wiki/Operator_(mathematics)#Linear_operators) means that (a)&#x00A0;they are straighforward to manipulate and (b)&#x00A0;typically do not need their operands enclosed in brackets.

[^EAbuse]: Describing $\text{E}f$ as 'expected' deaths when it is actually a random variable is an abuse of terminology and -- unsurprisingly -- repeatedly gives rise to confusion. But it is so ensconced that I feel using a different term would be even more confusing. I'll used 'expected' in inverted commas to refer to the random variable $\text{E}f$ and without inverted commas to refer expectation.

*If $\mu$ is the true mortality* then, *for any factor $f$*, the following are mathematical identities:

$$\begin{aligned}
\mathbb{E}\big(\text{A}f-\text{E}f\big)&=0
\\[0.5em]
\text{Var}\big(\text{A}f-\text{E}f\big)&=\mathbb{E}\big(\text{E}f^2\big)
\end{aligned} \tag{1}$$

where $\mathbb{E}$ -- in contrast to $\text{E}$ -- is true expectation[^EAbuse].

!!! info "Insight 1"

    These are the [*canonical*](https://en.wikipedia.org/wiki/Canonical_form) definitions of A and E in continuous time.
    
    Confusion -- usually over $\text{E}$ vs $\mathbb{E}$ -- and spurious complexity can arise over the interpretation of actual and expected deaths when using other definitions.

!!! info "Insight 2"

    If a model is true then we *expect* actual and 'expected' deaths to be equal
    
    - *over any subset* of the experience data, and
    - *weighted by any factor* -- this seems to surprise some practitioners.

!!! info "Insight 3"

    As well as defining A−E, we can *use the same machinery* to estimate its uncertainty.

### A over E

The classic diagnostic when reviewing mortality experience is to compare [actual vs expected (weighted) deaths](https://www.actuarialstandardsboard.org/glossary/actual-to-expected-ae-ratio/) on a prior mortality model, where the weight[^Weights] $w(i,t)$ is a non-negative factor.

[^Weights]: Typical weights are 'lives', i.e. 1 (unweighted), and 'amounts', which means benefit amount revalued (a)&#x00A0;for consistency between exposures and deaths and (b)&#x00A0;comparability over time.

The above variance result suggests the normal approximation

$$\text{A}w / \text{E}w \sim \text{N}\Big(1,\text{E}w^2 / (\text{E}w)^2\Big) \tag{2}$$

in which case the diagnostic it to compare A/E[^LogAOverE] to 100% and assess the difference relative to $\pm\sqrt{\text{E}w^2/(\text{E}w)^2}$.

[^LogAOverE]:

    Or, in log&#x202F;*μ* space,

    $$\log\!\big(\text{A}w / \text{E}w\big) \sim \text{N}\big(1,\text{E}w^2 / (\text{A}w\,\text{E}w)\big) \tag{3}$$

    In the cases where they differ materially, the standard A/E will be materially different from 100% in which case the diagnostic will be failed under either approach.

!!! info "Insight 4"

    the above definitions of A and E lead directly to a well-defined and parsimonious measure of weighted A/E itself *and* an estimate of its uncertainty.

!!! info "Insight 5"

    In the lives-weighted case, $\text{E}w^2 = \text{E}w$ and hence the variance estimate simplifies to $1 / \text{E}w$. There are two things to avoid:
    
    1. The lives-weighted variance estimate should not be used to estimate the variance of a weighted A/E because it will always understate it[^WeightVariance].

    [^WeightVariance]: Provided the $w$ exhibits some variation over the experience data, it is a [*mathematical* inequality](https://en.wikipedia.org/wiki/Cauchy%E2%80%93Schwarz_inequality) that $\text{E}w^2 \cdot \text{E}1\gt (\text{E}w)^2$.

    1. If the assumption that all statistics are lives-weighted is built into an implementation then that implementation will struggle when actual weighted results are required.

!!! info "Insight 6"

    Providers of grouped deaths and exposure data often provide it weighted by both lives and amounts. What they almost always overlook -- including, regrettably, [the CMI (paywalled)](https://www.actuaries.org.uk/documents/cmi-wp190-dataset) -- is that the amounts-weighted data should also include exposure data *weighted by amount squared*.

### Measures and partitions

$\text{A}$ and $\text{E}$ are both [*measures*](https://en.wikipedia.org/wiki/Measure_(mathematics)) over experience data[^ExpDims]. This is an important point -- it means that a linear combination of $\text{A}$ and $\text{E}$ gives the same result when summed over a partitioned experience dataset *regardless of how it is partitioned*.

[^ExpDims]: There are two axes for assessing whether experience data intersect, *identity* and *time*. Specifically, the data $(i_1,\varepsilon_1)$ and $(i_2,\varepsilon_2)$ intersect iff both

    - $i_1$ and $i_2$ relate to the same individual (*identity*), and
    - the exposure time intervals for $\varepsilon_1$ and $\varepsilon_2$ intersect (*time*).

    Time intersection can be determined direct from the data. Identity intersection requires either $i$ to contain a [UID](https://en.wikipedia.org/wiki/Unique_identifier) or external knowledge.

!!! info "Insight 7"

    If you use $\text{A}$ and $\text{E}$ you can fearlessly[^ApologiesRust] split or combine experience data in any way *provided the data does not intersect with itself*.

    In particular, there is *no need*

    - *to track individuals across experience datasets for different time periods*[^Tracking], or
    - *for the time periods to be contiguous*[^Contiguity].

[^ApologiesRust]: With apologies to [Rust](https://doc.rust-lang.org/book/ch16-00-concurrency.html).

[^Tracking]: Tracking individuals across experience datasets for different time periods can make sense as a data check or simply trying to understand the data.

[^Contiguity]: Gaps in data are equivalent to setting the weight to zero for the non-included time periods.

!!! info "Insight 8"

    If you want your analysis and model fitting to be *performant* then consider
    
    - using operations based on $\text{A}$ and $\text{E}$, and
    - running the code implementing those operations *in parallel* on partitions of the data.

### Log-likelihood and model fitting

We can define the (weighted[^WeightedL]) log-likelihood of an experience dataset for a specified mortality model $\mu$ directly in terms of the $\text{A}$ and $\text{E}$ operators as

[^WeightedL]:  Weighting log-likelihoods, i.e. placing more statistical weight on some data than others, is seen as controversial in some quarters. If you are uncomfortable with the notion of weighting then read the rest of this note assuming that only lives weights are used, i.e. the weight is only ever $0$ or $1$. Even if you object to weighting log-likelihood, I suggest there are cases where having the ability to weight log-likelihood is helpful and therefore it should be builtin as a capability to an experience analysis system.

$$L=\text{A}w\log\mu-\text{E}w \tag{4}$$

$L$ is a linear combination of $A$ and $E$, and so it too can be summed over a partitioned experience dataset *regardless of how it is partitioned*.

In order to make progress, assume that the mortality model is a [proportional hazards model](https://en.wikipedia.org/wiki/Proportional_hazards_model), i.e.

$$\mu=\mu^\text{ref}\exp\!\big(\beta^\text{T}X\big)$$

where $X$ is a vector[^LinAlg] of factors ('covariates') and $\mu^\text{ref}$ is a reference mortality model, both of which are independent of $\beta$.

[^LinAlg]: I use the following notation for linear algebraic:

    - $^\text{T}$ means vector or matrix [transpose](https://en.wikipedia.org/wiki/Transpose).
    - $\beta^\text{T}X$ means the ['dot' or 'scalar' product](https://en.wikipedia.org/wiki/Dot_product) of vectors $\beta$ and $X$, i.e. $\beta^\text{T}X=\sum_j \beta_j X_j$.
    - Matrices are shown in bold caps, e.g. $\mathbf{A}$.

!!! info "Insight 9"

    Proportional hazards models are both
    
    - sufficiently powerful to cope with almost all practical mortality modelling problems, and
    - highly tractable.
    
    They are also arguably *the* canonical mortality model[^CanonPH] in a log-likelihood fitting context.

[^CanonPH]:

    I suggest that the proportional hazards model is *the* canonical mortality model in the sense that differentiating the log-likelihood results in 

    $$\text{A}w\frac{1}{\mu}\frac{\partial \mu}{\partial \beta} = \text{E}w\frac{1}{\mu}\frac{\partial \mu}{\partial \beta}$$

    and if we define the common operand of both $\text{A}$ and $\text{E}$ as

    $$X=\frac{1}{\mu}\frac{\partial \mu}{\partial \beta}$$

    it is natural to require that $X$ be independent of $\beta$, in which case the proportional hazards model falls out directly as the solution, i.e.

    $$\mu=\mu^\text{ref}\exp\!\big(\beta^\text{T}X\big)$$

We estimate $\beta$ by [maximising the log-likelihood](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) with respect to $\beta$, or equivalently. solving for the (vector) condition $\partial L / \partial \beta=0$. For the proportional hazards model, this condition is simply

$$\text{A}wX = \text{E}wX$$

!!! info "Insight 10"

    If any single covariate in $X$ is a constant (i.e. independent of individuals and time) then fitting this model guarantees $Aw/Ew=1$ for the experience data to which the model has been fitted.
    
    In real world models, all covariates depend on age, but that dependence is sufficiently weak / smeared that fitted, working models still result in $Aw/Ew\approx1$.

### Parameter uncertainty

> I'll use $'$ to denote $\partial / \partial\beta$ from here on.

We can estimate the uncertainty of the best estimate vector of parameters $\hat\beta$ using[^BurnhamAnderson]

$$\text{Var}\big(\hat\beta\big)\approx\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1} \tag{5}$$

where the square matrices $\mathbf{I}=-L''(\hat\beta)$ and $\mathbf{J}$ estimates $\mathbb{E}\,L'(\beta)^\text{T}L'(\beta)$.

For a lives-weighting, $\mathbf{I}\approx\mathbf{J}$ and hence $\text{Var}\big(\hat\beta\big)\approx\mathbf{I}^{-1}$.

For the proportional hazards model, we have the elegant result

$$\begin{aligned}
\mathbf{I}&=\text{E}wXX^\text{T}
\\[1em]
\mathbf{J}&=\text{E}w^2XX^\text{T}
\end{aligned}$$

### Generalised AIC

The generalisation of the [Akaike information criterion](https://en.wikipedia.org/wiki/Akaike_information_criterion) (AIC) under this framework is equivalent[^AICDef] to using the following penalised log-likelihood:

[^AICDef]:

    The AIC is conventionally defined as $\text{AIC}=-2L_\text{P}$ for consistency with the normal distribution and [deviance](https://en.wikipedia.org/wiki/Deviance_(statistics)). I think that the $-2$ multiplier is unhelpful because it
    
    - makes the formula more complicated,
    - obscures the link between the AIC and log-likelihood, and, in particular, the justification of the AIC as an *unbiased* estimator of the [Kullback-Leibler divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) from the (notional) true underlying model, and

    - leads to unnecessary factors of $2$ or $\tfrac{1}{2}$ in its application -- see e.g. [guidance on application in the Wikipedia AIC article](https://en.wikipedia.org/wiki/Akaike_information_criterion#How_to_use_AIC_in_practice) or [this article by Burnham & al](https://www.ericlwalters.org/Burnham_etal_2011.pdf).

$$L_\text{P}=L(\hat\beta)-\text{tr}(\mathbf{I}^{-1}\mathbf{J})$$

For lives-weighted modelling, this collapses to the conventional form

$$L_\text{P}=L(\hat\beta)-\dim(\beta)$$

!!! info "Insight 11"

    This result extends the conventional AIC to the weighted log-likelihood case.

### Overdispersion

In the real world, mortality experience data typically exhibits greater variance than implied by the [Poisson distribution](https://en.wikipedia.org/wiki/Poisson_distribution) that the above approach assumes. Specifically

$$\text{Var}\big(\text{A}w-\text{E}w\big) > \mathbb{E}\big(\text{E}w^2\big)$$

This is commonly known as [overdispersion](https://en.wikipedia.org/wiki/Overdispersion#Poisson) and can be caused by unaccounted for stochastic variation or data issues[^WeightOverdispersion]. 

[^WeightOverdispersion]: Note that this definition specifically excludes the additional variance of parameter estimates arising as a result of using a weighted log-likelihood.

Overdispersion does not usually affect the parameter estimates themselves, but it does affect their estimated variance, potentially leading to the AIC potentially over-fitting models if it is not accounted for.

!!! info "Insight 12"

    Not allowing for overdispersion will, in practice, result in over-fitted mortality models.

The simplest *ad hoc* approach is to divide the log-likelihood by an estimate of the overdispersion, $\Omega$, in which case it becomes a [quasi-log-likelihood](https://en.wikipedia.org/wiki/Quasi-likelihood), in which case the above numbered equations are adjusted as follows:

$$\begin{array}{lclr}
\text{Var}\big(\text{A}f-\text{E}f\big)=\mathbb{E}\big(\text{E}f^2\big)
& \mapsto &
\text{Var}\big(\text{A}f-\text{E}f\big)=\Omega\,\mathbb{E}\big(\text{E}f^2\big)
&\text{(1)}
\\[1em]
\text{A}w / \text{E}w \sim \text{N}\Big(1,\text{E}w^2 / (\text{E}w)^2\Big)
& \mapsto &
\text{A}w / \text{E}w \sim \text{N}\Big(1,\Omega\,\text{E}w^2 / (\text{E}w)^2\Big)
&\text{(2)}
\\[1em]
\log\!\big(\text{A}w / \text{E}w\big) \sim \text{N}\big(1,\text{E}w^2 / (\text{A}w\,\text{E}w)\big)
& \mapsto &
\log\!\big(\text{A}w / \text{E}w\big) \sim \text{N}\big(1,\Omega\,\text{E}w^2 / (\text{A}w\,\text{E}w)\big)
&\text{(3)}
\\[1em]
L=\text{A}w\log\mu-\text{E}w
& \mapsto &
L=\Omega^{-1}\big(\text{A}w\log\mu-\text{E}w\big)
&\text{(4)}
\\[1em]
\text{Var}\big(\hat\beta\big)\approx\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}
& \mapsto &
\text{Var}\big(\hat\beta\big)\approx\Omega\,\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}
&\text{(5)}
\end{array}$$

Without any prior information, I'd suggest starting with $\Omega=2$.