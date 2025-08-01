---
#draft: true 
date: 2025-08-05
authors:
  - timgord
categories:
  #- Coding
  #- Forecasting
  - Maths & stats
  - Mortality & longevity
  #- Presentation

#tags:
#  - Coding
#  - Mortality
#  - Forecasting
#  - Standards

# The local and server slugifications produce *different* results when there are dashes in the item title.
#slug: mortality-a-over-e

# Don't forget to add the <!-- more --> separator!!!
---

# Mortality: A over E

/// admonition | Why 'A over E'?
    type: info
    attrs: {class: "inline end"}

'A over E' literally refers to 'actual' deaths divided by 'expected' deaths as a measure of how mortality experience data compares with a mortality.

In practice, 'A over E' is often interpreted as the whole statistical caboodle, which is what I'll do here.
///

<!--
[overdispersion](/2025-08/mortality-measures-matter/#Def-overdispersion)
[E2R](/2025-08/mortality-measures-matter/#Def-E2R)
[experience dataset](/2025-08/mortality-measures-matter/#Def-exp-data)
[variable](/2025-08/mortality-measures-matter/#Def-variable)
[mortality](/2025-08/mortality-measures-matter/#Def-mortality)
[$\text{A}$ and $\text{E}$ operators](/2025-08/mortality-measures-matter/#Def-AE-ops)
-->

In the [previous article](/2025-08/mortality-measures-matter/) we defined [experience data](/2025-08/mortality-measures-matter/#Def-exp-data), [variables](/2025-08/mortality-measures-matter/#Def-variable) and [mortality](/2025-08/mortality-measures-matter/#Def-mortality) with respect to that data, and the [$\text{A}$ and $\text{E}$ operators](/2025-08/mortality-measures-matter/#Def-AE-ops).

In this article we'll start putting that into practice.

## The 'expected' result

When the mortality $\mu$ is the *true mortality* then *for any variable $f$* it is easy to show that

$$\mathbb{E}\big(\text{A}f-\text{E}f\big)=0\tag{1}$$

where $\mathbb{E}$ is true expectation.

This is hardly a surprise but what is sometimes forgotten is that this is true

- *for any weight*, even if that that weight was used to fit the mortality in question, and
- *for any subset*, provided the choice of subset does not depend on E2R information.

<!-- more -->

--8<-- "snippets/mortality-series-list.md"

/// admonition | Insight 4. The expected value of *A*−*E* is zero
    type: insight
    attrs: {id: "Insight4"}

If $\mu$ is the true mortality then the expected value of $\text{A}f-\text{E}f$ is zero

- *for any variable $f$* (even if $f$ was used to fit the mortality in question), and
- *for any subset* of the experience data (provided the choice of subset does not depend on E2R information).

[[All mortality insights](/collated-mortality-insights/#Insight4)]
///

## The 'variance' result

There is a second result that is reasonably well known by actuaries. When the mortality $\mu$ is the *true mortality* then *for any variable $f$* with a little more work[^IntegrationByParts] we can show that

[^IntegrationByParts]: Nothing more than integration by parts.

$$\text{Var}\big(\text{A}f-\text{E}f\big)=\mathbb{E}\big(\text{E}f^2\big) \tag{2}$$

A couple of observations:

1. If you have the machinery in place to calculate $\text{A}f$ and $\text{E}f$ then the above means that you are also immediately in a position to estimate its variance.
1. The above does *not* allow for [overdispersion](/2025-08/mortality-measures-matter/#Def-overdispersion). In other words, it is typically an *underestimate* of variance.

This is a generalisation of the well-known lives-weighted (i.e. unweighted) result[^LivesVariance] that the variance of *A*−*E* is *E*.

[^LivesVariance]: The general lives-weighting is $f\in\{0,1\}$, for which $f^2=f$ and hence the variance estimate simplifies to $\text{E}f$.

/// admonition | Insight 5. The *same machinery* that defines *A*−*E* can be used to estimate its uncertainty
    type: insight
    attrs: {id: "Insight5"}

If $\mu$ is the true mortality then the variance of $\text{A}f-\text{E}f$ equals the expected value of $\text{E}f^2$.

(This is before allowing for overdispersion.)

[[All mortality insights](/collated-mortality-insights/#Insight5)]
///

Over the years, I've seen the lives-weighted case can lead practitioners into making some invalid assumptions or bad decisions:

1. An unadjusted lives-weighted variance estimate should *not* be used to estimate the variance of a weighted A/E because it will always understate it[^WeightVariance].

1. Building in the assumption that all statistics are lives-weighted into an implementation means that that implementation will struggle when weighted results are required in the future.

1. When the experience data is provided in the form of *grouped* deaths and exposures, it is reasonably common for this data to be provided weighted both by lives and by benefit amount. But what is almost always overlooked[^ShouldKnowBetter] is that the amounts-weighted exposure data should also include exposure data *weighted by amount squared*.

[^WeightVariance]: Provided the $f$ exhibits some variation over the experience data, it is a [mathematical truth](https://en.wikipedia.org/wiki/Cauchy%E2%80%93Schwarz_inequality) that $\text{E}f^2\cdot\text{E}1\gt (\text{E}f)^2$.

[^ShouldKnowBetter]: By parties that really should know better.

## Diagnostics

With a mean and a variance, we are now in a position to take a first stab at A/E diagnostics, i.e. residuals and  confidence intervals.

The Pearson residual is (*actual*&#x00A0;−&#x00A0;*expected*)&#x202F;/&#x202F;(*estimated&#x00A0;standard&#x00A0;deviation*). In our case, *actual* is $\text{A}f-\text{E}f$, *expected* is $0$, and *estimated standard deviation* is $\sqrt{\text{E}f^2}$, and so the Pearson residual is

$$r_\text{P}=\frac{\text{A}f-\text{E}f}{\sqrt{\text{E}f^2}}$$

Another obvious A/E diagnostic is the literal one, i.e. divide equation $\text{(1)}$ by $\text{E}f$ and assume that central limit theorem and low variance of $\text{E}f$ make it reasonable to assume

$$\frac{\text{A}f}{\text{E}f}\sim N\!\left(1,\;\frac{\text{E}f^2}{(\text{E}f)^2}\right)$$

In other words, review $\text{A}f/\text{E}f$ and compare its difference from $1$ with (a multiple of) $\pm\sqrt{\text{E}f^2} / \text{E}f$.

/// admonition | Insight 6. A/E variance increases with concentration
    type: insight
    attrs: {id: "Insight6"}

$\sqrt{\text{E}w^2} / \text{E}w$, where $w\ge0$ is a useful and recurring measure of effective concentration in relation to mortality uncertainty. It implies that the more concentrated the experience data (in some sense) then the greater the variance of observed mortality.

Using unweighted variance without adjustment to estimate weighted statistics will likely *understate* risk.

[[All mortality insights](/collated-mortality-insights/#Insight6)]
///

The above diagnostics are ok in practice, but they have some drawbacks:

- We've relied on $\sqrt{\text{E}f^2}\gg \text{E}f$ and the central limit theorem, which will break down for datasets with fewer / more concentrated weighted deaths.
- The implied confidence intervals can include negative values!

We can do better and so we'll revisit A/E diagnostics at a later date. A pre-requisite is log likelihood, which is the subject of the next article.
