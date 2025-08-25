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

Caveat: $w$ is an *ad hoc* reallocation of log-likelihood; it is *not* relevance.

[[Original article](/2025-08/mortality-suddenly-aic#Insight9)]
///

/// admonition | Insight 10. A penalised log-likelihood for a proportional hazards mortality model is available in closed form for any *ad hoc* log-likelihood weight
    type: insight
    attrs: {id: "Insight10"}

$$L_\text{P}= L(\hat\beta)-\text{tr}\big(\mathbf{J}\mathbf{I}^{-1}\big)$$

where $\hat\beta$ is the maximum likelihood estimator of the covariate weights, $X$ is the vector of covariates, $L$ is the log-likelihood (which has *already* been adjusted for overdispersion), $w\ge0$ is the log-likelihood weight, $\mathbf{I}=\text{E}wXX^\text{T}$ and $\mathbf{J}=\text{E}w^2XX^\text{T}$.

Caveat: $w$ is an *ad hoc* reallocation of log-likelihood; it is *not* relevance.

[[Original article](/2025-08/mortality-suddenly-aic#Insight10)]
///

