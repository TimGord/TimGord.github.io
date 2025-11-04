# Collated mortality insights

These are the collated mortality insights from all my blog articles.

<!-- 
/2025-08/mortality-measures-matter/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 1. Always allow for overdispersion
    type: insight
    attrs: {id: "Insight1"}

If you don't allow for overdispersion then you *will* underestimate uncertainty and overfit models.

[[Original article](/2025-08/mortality-measures-matter#Insight1)]
///

/// admonition | Insight 2. Experience data is '[measurable](https://en.wikipedia.org/wiki/Measurable_space)'
    type: insight
    attrs: {id: "Insight2"}

Provided we use [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)), we'll always get the same answer *regardless of how an experience dataset is partitioned*.

In particular, there is no need

- *for experience time periods to be contiguous*[^OmitPandemic] -- the sole requirement is that elements of the experience datasets do not intersect, or
   - *to track individuals across experience datasets relating to different time periods*[^Tracking].

[^OmitPandemic]: An obvious example is excluding mortality experience from the height of the COVID-19 pandemic, potentially resulting in *non-contiguous* data from before and after the excluded time period.

[^Tracking]: Tracking individuals across experience datasets for different time periods may however be a very sensible data check.

[[Original article](/2025-08/mortality-measures-matter#Insight2)]
///

/// admonition | Insight 3. The continuous time definitions of A and E are canonical
    type: insight
    attrs: {id: "Insight3"}

The continuous time definitions of $A$ and $E$ are measures and *the* [canonical](https://en.wikipedia.org/wiki/Canonical_form) definitions of actual and expected deaths.

Other definitions can lead to confusion -- usually over $\text{E}$ vs true expectation -- and spurious complexity.

[[Original article](/2025-08/mortality-measures-matter#Insight3)]
///

<!-- 
/2025-08/mortality-a-over-e/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 4. The expected value of *A*−*E* is zero
    type: insight
    attrs: {id: "Insight4"}

If $\mu$ is the true mortality then the expected value of $\text{A}f-\text{E}f$ is zero, i.e.

$$\mathbb{E}\big(\text{A}f-\text{E}f\big)=0$$

- *for any variable $f$* (even if $f$ was used to fit the mortality in question), and
- *for any subset* of the experience data (provided the choice of subset does not depend on E2R information).

[[Original article](/2025-08/mortality-a-over-e#Insight4)]
///


/// admonition | Insight 5. The *same machinery* that defines *A*−*E* can be used to estimate its uncertainty
    type: insight
    attrs: {id: "Insight5"}

If $\mu$ is the true mortality then, *before allowing for overdispersion*, the variance of $\text{A}f-\text{E}f$ equals the expected value of $\text{E}f^2$, i.e.

$$\text{Var}\big(\text{A}f-\text{E}f\big)=\mathbb{E}\big(\text{E}f^2\big)$$

Allowing for overdispersion $\Omega$, this becomes

$$\text{Var}\big(\text{A}f-\text{E}f\big)=\Omega\,\mathbb{E}\big(\text{E}f^2\big)$$

Caveat: $f$ is an *ad hoc* reallocation of log-likelihood; it is *not* [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance). For the version of this insight that *does* take account of relevance, see [Insight&#xA0;17](/collated-mortality-insights#Insight17).

[[Original article](/2025-08/mortality-a-over-e#Insight5)]
///

/// admonition | Insight 6. A/E variance increases with concentration
    type: insight
    attrs: {id: "Insight6"}

$\sqrt{\text{E}w^2} / \text{E}w$, where $w\ge0$ is a useful and recurring measure of effective concentration in relation to mortality uncertainty. It implies that the more concentrated the experience data (in some sense) then the greater the variance of observed mortality.

Using unweighted variance without adjustment to estimate weighted statistics will likely *understate* risk.

[[Original article](/2025-08/mortality-a-over-e#Insight6)]
///

<!-- 
/2025-08/mortality-log-likelihood/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 7. Log-likelihood can be defined directly in terms of the $\text{A}$ and $\text{E}$ operators
    type: insight
    attrs: {id: "Insight7"}

The log-likelihood written in terms of the $\text{A}$ and $\text{E}$ operators is

$$L=\text{A}w\log\mu-\text{E}w$$

where $w\ge0$ is the weight variable.

(This is *before* allowing for overdispersion.)

[[Original article](/2025-08/mortality-log-likelihood#Insight7)]
///

<!-- 
/2025-08/mortality-proportional-hazards/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 8. Proportional hazards models are probably all you need for mortality modelling
    type: insight
    attrs: {id: "Insight8"}

The proportional hazards model

$$\mu(\beta) = \mu^\text{ref}\exp\Big(\beta^\text{T}X\Big)$$

is

- highly tractable, and
- sufficiently powerful to cope with almost all practical mortality modelling problems.

[[Original article](/2025-08/mortality-proportional-hazards#Insight8)]
///

<!-- 
/2025-08/mortality-suddenly-aic/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 9. An estimate of the variance of the fitted parameters for a proportional hazards mortality model is available in closed form for any *ad hoc* log-likelihood weight
    type: insight
    attrs: {id: "Insight9"}

$$\text{Var}\big(\hat\beta\big)\mathrel{\hat=} \Omega\,\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}$$

where $\hat\beta$ is the maximum likelihood estimator of the covariate weights, $X$ is the vector of covariates, $w\ge0$ is the log-likelihood weight, $\mathbf{I}=\text{E}wXX^\text{T}$, $\mathbf{J}=\text{E}w^2XX^\text{T}$ and $\Omega$ is overdispersion

Caveat: $w$ is an *ad hoc* reallocation of log-likelihood; it is *not* [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance). For the version of this insight that *does* take account of relevance, see [Insight&#xA0;17](/collated-mortality-insights#Insight17).

[[Original article](/2025-08/mortality-suddenly-aic#Insight9)]
///

/// admonition | Insight 10. A penalised log-likelihood for a proportional hazards mortality model is available in closed form for any *ad hoc* log-likelihood weight
    type: insight
    attrs: {id: "Insight10"}

$$L_\text{P}= L(\hat\beta)-\text{tr}\big(\mathbf{J}\mathbf{I}^{-1}\big)$$

where $\hat\beta$ is the maximum likelihood estimator of the covariate weights, $X$ is the vector of covariates, $L$ is the log-likelihood (which has *already* been adjusted for overdispersion), $w\ge0$ is the log-likelihood weight, $\mathbf{I}=\text{E}wXX^\text{T}$ and $\mathbf{J}=\text{E}w^2XX^\text{T}$.

Caveat: $w$ is an *ad hoc* reallocation of log-likelihood; it is *not* [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance). For the version of this insight that *does* take account of relevance, see [Insight&#xA0;17](/collated-mortality-insights#Insight17).

[[Original article](/2025-08/mortality-suddenly-aic#Insight10)]
///

<!-- 
/2025-09/mortality-overdispersion-and-quasi-log-likelihood/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 11. Adjusting *globally* for overdispersion is reasonable and straightforward
    type: insight
    attrs: {id: "Insight11"}

If $\Omega$ is *global* overdispersion then:

1. A standard method for allowing for overdispersion is to scale log-likelihood by $\Omega^{-1}$ and variances by $\Omega$.

1. Suitable default values for mortality experience data are $2\le\Omega\le3$.

1. Use the *same $\Omega$ for all candidate models being tested*, including when $\Omega$ is being estimated from the experience data at hand.

[[Original article](/2025-09/mortality-overdispersion-and-quasi-log-likelihood#Insight11)]
///

<!-- 
/2025-10/mortality-variation-in-pension-plan-mortality/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 12. Rating factors must be *coherent*
    type: insight
    attrs: {id: "Insight12"}

In order for a function of information associated with individuals to be valid as a rating factor, it must be *coherent*, which means:

1. No foreknowledge of death
1. Correspondence between exits and survivors
1. Comparability between individuals
1. Comparability by time

[[Original article](/2025-10/mortality-incoherent-rating-factors#Insight12)]
///

/// admonition | Insight 13. Take care when using pension as a rating factor
    type: insight
    attrs: {id: "Insight13"}

Be wary of phrases like 'just use pension as a covariate' because it trivialises the problems involved in making pension a *coherent* rating factor:

- Pensions for individuals in different pension plans are *not* directly comparable. For general pension plan mortality models consider using [leave-one out cross validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)#Leave-one-out_cross-validation) to understand this risk and/or using an alternative approach.
- Pensions as at date of exit need careful adjustment to be consistent with pensions of survivors (which can be non trivial for UK DB plans).
- Pensions for actives require additional consideration in relation to potential future accrual.
- Consideration needs to be given to whether or how to adjust pensions for inflation (typically since retirement). This is more of an issue in pension systems where indexation of pensions in payment is less common (e.g. the USA).
- Do not assume that longevity always increases with benefit amount.

[[Original article](/2025-10/mortality-incoherent-rating-factors#Insight13)]
///

<!-- 
/2025-10/mortality-pensioner-mortality-variation/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 14. The bulk of pension plan mortality variation can be captured on a monotonic one dimensional scale
    type: insight
    attrs: {id: "Insight14"}

Modelling base mortality for UK DB pension plans can be reasonably reduced to modelling a single parameter for each of male pensioners, female retirees and female dependants, i.e.

$$\mu_{it}(\beta)= \mu_{it}^\text{ref} \exp\big(\beta\psi_x\big)$$

where $x$ is age as a function of birth date from individual data $i$ and time $t$, $\mu_{it}^\text{ref}$ is a common base mortality and $\psi_x$ is a common (non-negative) pattern of mortality variation by age that tends to zero at high ages.

[[Original article](/2025-10/mortality-pensioner-mortality-variation#Insight14)]

///

<!-- 
/2025-10/mortality-good-things-come-to-those-who-weight-ii/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->
/// admonition | Insight 15. Weighted log-likelihood *automatically* estimates liabilities correctly for single scalar parameter models when provided with relevance
    type: insight
    attrs: {id: "Insight15"}

If (a)&#xA0;a mortality model has a single scalar parameter and (b)&#xA0;[relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance) is provided then maximising log-likelihood weighted by

$$w_{it}=\sum_{j\in\text{Val}} r_{it}^{jt_0} \, I_j^{-1} v'_j$$

*automatically* results in the best estimate of the present value of liabilities.

In the above,

- $r_{it}^{jt_0}$ is the relevance of the log-likelihood of the E2R of individual $i$ at time $t$ to individual $k$ in the valuation data as at the valuation date $t_0$,
- $I_j$ is the relevant information matrix for valuation individual $j$, and
- $v'_j$ is derivative of liability value for valuation individual $j$ with respect to the model parameter $\beta$.

For further definitions, see [article body](/2025-10/mortality-good-things-come-to-those-who-weight-ii/#starting-point).

[[Original article](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight15)]

///


/// admonition | Insight 16. A different weight is required to determine uncertainty in the presence of relevance
    type: insight
    attrs: {id: "Insight16"}

The log-likelihood weight to determine uncertainty that corresponds to the best estimate weight in [Insight&#xA0;15](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight15) is


$$u_{it}=\sum_{j\in\text{Val}} \sqrt{r_{it}^{jt_0}} \, I_j^{-1} v'_j$$

[[Original article](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight16)]

///

<!-- 
/2025-11/mortality-good-things-come-to-those-who-weight-iii/ 
●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
-->

/// admonition | Insight 17. Always allow for time-based relevance
    type: insight
    attrs: {id: "Insight17"}

Allowing for time-based relevance, e.g. using 

$$r_s^t=\exp\!\big(\!-\phi\,\big|t-s\big|\big)$$

where $s$ and $t$ are dates (measured in years) and $\phi>0$, is to be preferred in all contexts because

- it automatically allows for the decay in relevance as time elapses, and
- compared with fixed windows, leaves models less sensitive to the falling away of more historical data.

If relevance is purely time-based then this can be accomplished simply by scaling the data.

[[Original article](/2025-11/mortality-good-things-come-to-those-who-weight-iii#Insight17)]

///


/// admonition | Insight 18. Use relevance for calibrating and selecting DB pensioner base mortality models 
    type: insight
    attrs: {id: "Insight18"}

Using the weights by $w$ and $u$ as defined in Insights&#xA0;[15](/collated-mortality-insights#Insight15) and [16](/collated-mortality-insights#Insight16) respectively to calibrate and select DB pensioner base mortality models 

- takes explicit account of liability impact, and

- defaults to sensible results regardless of the quantum of experience data available.

The following Insights need to restated to accommodate relevance:

- [Insight&#xA0;5](/collated-mortality-insights#Insight5) (allowing for overdispersion $\Omega$) becomes

    $$\text{Var}\big(\text{A}w-\text{E}w\big)=\Omega\,\mathbb{E}\big(\text{E}u^2\big)$$

- The equations for [Insights&#xA0;9](/collated-mortality-insights#Insight9) and [Insights&#xA0;10](/collated-mortality-insightsaic#Insight10) are unchanged as

    $$\begin{aligned}
    \text{Var}\big(\hat\beta\big)\mathrel{\hat=} \Omega\,\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}
    \\[1em]
    L_\text{P}= L(\hat\beta)-\text{tr}\big(\mathbf{J}\mathbf{I}^{-1}\big)
    \end{aligned}$$

    But $\mathbf{J}$ is redefined as $\text{E}u^2XX^\text{T}$, i.e. weighted by $u^2$ rather than $w^2$.

[[Original article](/2025-11/mortality-good-things-come-to-those-who-weight-iii#Insight18)]

///

/// admonition | Insight 19. Prefer amounts-weighted to lives-weighted log-likelihood
    type: insight
    attrs: {id: "Insight19"}

For DB pensioner mortality analysis, prefer log-likelihood weighted by pension amount to lives-weighted.

[[Original article](/2025-11/mortality-good-things-come-to-those-who-weight-iii#Insight19)]

///
