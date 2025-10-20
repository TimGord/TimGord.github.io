---
#draft: true 
date: 2025-08-04
authors:
  - timgord
#comments: true
categories:
  #- Coding
  #- Forecasting
  - Maths & stats
  - Mortality & longevity
  #- Presentation

##comments: true

#tags:
#  - Coding
#  - Mortality
#  - Forecasting
#  - Standards

# The local and server slugifications produce *different* results when there are dashes in the item title.
#slug: mortality-measures-matter

# Don't forget to add the <!-- more --> separator!!!
---

# Mortality: Measures matter

This is the first in a series of articles outlining *mortality experience analysis fundamentals*, by which I mean estimating the underlying mortality for individuals in a defined group (e.g. members of a pension plan) from mortality experience data.

This will be fairly technical, but I'll aim

- <span id="Ref-concision">to be concise</span>,
- to pull out the key insights, including what does and doesn't matter in practice, and
- to map concepts one-to-one to the process of actually carrying out a mortality experience analysis or calibrating and selecting mortality models.

<!-- more -->

A lot of this is reasonably well known, but it is not always available in one place or easily accessible, and sometimes key points are omitted. It won't be all plain sailing, and there'll be a few potentially contentious and maybe even surprising points along the way.

In this first article, I'll set out the foundations.

--8<-- "snippets/mortality-series-list.md"

## Definitions

An <span id="Def-E2R">**exposed-to-risk**</span> (E2R) for an individual comprises

- an exposure period $[\nu,\tau)$[^Interval][^ContinuousTime] throughout which the individual was alive, and
- an indicator $\delta$, which is $1$ if the individual died at $\tau$ or else $0$.

I'll write an E2R as $\varepsilon=(\nu,\tau,\delta)$.

For each individual we also have a set of <span id="Def-fact">**facts**</span>, $i$, known at the time of the analysis and which are time invariant.

If a fact relates to something that would have happened except that the individual died, e.g. pension in payment in 2025 for an individual who died in 2024, then we assume this fact is determined *as if the individual had lived*. This is critical for unbiased analysis and is sometimes referred to as 'the principle of correspondence'[^NoCheating].

[^NoCheating]: From a technical point of view, this principle also means mortality models can't cheat simply by looking at the data.

An <span id="Def-exp-data">**experience dataset**</span> $\mathscr{E}$ comprises pairs of facts and E2Rs, i.e. $\{(i,\varepsilon)\}$ for which no E2Rs for the same individual overlap in time[^Deduplication].

A <span id="Def-variable">**variable**</span> is a real-valued function $f(i,t)$ of facts $i$ and time $t$[^ContinuityInTime]. A variable is *not* random -- by assumption, the sole source of stochasticity is whether individuals die or not, which is embedded in $\varepsilon$.

A <span id="Def-mortality">**mortality**</span>, $\mu$, is a strictly positive variable[^Terminal] that specifies the probability of an individual dying over an infinitessimal time interval $\text{d}t$ as $\mu(i,t)\,\text{d}t$, i.e. an independent[^Independence] [Bernoulli trial](https://en.wikipedia.org/wiki/Bernoulli_trial).

[^Interval]: The notation $[\nu,\tau)$ means the [interval](https://en.wikipedia.org/wiki/Interval_(mathematics)) $\{t\in\mathbb{R} \mid  \nu\le t \lt \tau\}$.

[^ContinuousTime]: I'll take it as a given that we should work in (some representation of) continuous time if at all possible. Otherwise we'd be (a)&#xA0;throwing away data and (b)&#xA0;creating additional cognitive load and potentially biased or even plain wrong results by having to make assumptions about averages.

[^Deduplication]: Easy to stipulate in theory, but data de-duplication is an essential and sometimes non-trivial part of real world mortality experience analysis.

[^ContinuityInTime]:

    We need to place some conditions on the dependence of a variable $f(i,t)$ on time $t$.
    
    The most general is that $f(i,t)$ is left-continuous with right limits in $t$, left continuity being required so that the value at death is consistent with the value in the immediately preceding exposure. But this level of generality is impractical for an actual implementation.
    
    A more useful real-world condition is that $f(i,t)$ is *smooth* in $t$ at the scale of numerical integration. We'll leave *smoothness* undefined for now, other than to state that, as a minimum, it implies [absolute continuity](https://en.wikipedia.org/wiki/Absolute_continuity) in $t$.

[^Terminal]: An implementation would also need a mortality to specify a terminal date or age by individual (because mortality tables stop), but we don't need that for this exposition.

[^Independence]: For the avoidance of doubt, we assume that these Bernoulli trials are independent by time and by individual.

## What is random?

We will assume that the mortality $\mu$ at hand is itself completely deterministic and the sole source of random variation is whether or not individuals die according to $\mu$.

Don't lose sight of the fact that this is a convenient fiction to make the problem tractable and that, in practice, *this is never true for mortality data*, not least because $\mu$ itself is also stochastic. <span id="Def-overdispersion">This manifests as observed variances being materially higher than would be predicted by a fitted Poisson distribution, and is known as **overdispersion**.</span>

/// admonition | Insight 1. Always allow for overdispersion
    type: insight
    attrs: {id: "Insight1"}

If you don't allow for overdispersion then you *will* underestimate uncertainty and overfit models.

[[All mortality insights](/collated-mortality-insights#Insight1)]
///

The good news is that we can fix things up to allow for overdispersion, which we'll get to in a later article. In the meantime, whenever you come across references to mortality variance or uncertainty (in this blog or anywhere else), a little voice in your head should be saying '*remember to allow for overdispersion*'.

## The measures that matter

<span id="Def-measure">
We can picture experience data as comprising infinitesimals $\text{d}(i,\varepsilon)$ that can be added up in a couple of different ways. The mathematical approach to this is to define [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)) on the data. The pay off is that *provided we use a measure* we can add up functions over experience data any way we like and we're guaranteed to end up with the same answer.[^Parallel]
</span>

[^Parallel]: The freedom to partition experience data may also present opportunities to run [calculations in parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel). (I suggest that your mortality experience calculations should be running in parallel at least somewhere along the line.)

/// admonition | Insight 2. Experience data is '[measurable](https://en.wikipedia.org/wiki/Measurable_space)'
    type: insight
    attrs: {id: "Insight2"}

Provided we use [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)), we'll always get the same answer *regardless of how an experience dataset is partitioned*.

In particular, there is no need

- *for experience time periods to be contiguous*[^OmitPandemic] -- the sole requirement is that elements of the experience datasets do not intersect, or
- *to track individuals across experience datasets relating to different time periods*[^Tracking].

[^OmitPandemic]: An obvious example is excluding mortality experience from the height of the COVID-19 pandemic, potentially resulting in *non-contiguous* data from before and after the excluded time period.

[^Tracking]: Tracking individuals across experience datasets for different time periods may however be a very sensible data check.

[[All mortality insights](/collated-mortality-insights#Insight2)]
///

I suggested above that there are two things we want to add up. If you’re a practitioner, you’ve already come across them (or at least an approximation to them) and, as we’ll see over this series, *pretty much every useful aspect of mortality experience analysis can be expressed directly in terms of them*:

<span id="Def-AE-ops">

1. **Actual deaths** is the sum of $f$ over deaths at time of death:

    $$\text{A}f=\sum_{(i,\varepsilon)\in \mathscr{E}}\delta_\varepsilon f(i,\tau_\varepsilon)$$

    Note that the variable, $f$, is evaluated at time of death.

1. <span id="Def-E-op">**Expected deaths**[^ExposureMeasure] with respect to mortality $\mu$ is the integral of $\mu$ times $f$ over all exposure periods:</span>

    $$\text{E} f=\sum_{(i,\varepsilon)\in \mathscr{E}}\int_{\nu_\varepsilon}^{\tau_\varepsilon}\!\mu(i,t)f(i,t)\,\text{d}t$$

</span>

[^ExposureMeasure]:

    We could alternatively define an 'exposure measure' as

    $$\text{S} f=\sum_{(i,\varepsilon)\in \mathscr{E}}\int_{\nu_\varepsilon}^{\tau_\varepsilon}\!f(i,t)\,\text{d}t$$

    While this is a simpler definition, it would
    
    - clutter up our notation because we'd end up writing $\text{S}\mu f$ everywhere instead of $\text{E}f$, and
    - obscure the symmetry between $\text{A}$ and $\text{E}$.

/// admonition | 'Expected' is not expectation
    type: danger
    attrs: {class: "inline end"}

$\text{E}f$ is a *random variable* (like $\text{A}f$), and so describing it as 'expected' deaths can give rise to confusion. But the practice is so ensconced that using a different term would be even more confusing.

The terminology arises because 'expected deaths' is typically an estimate of expected deaths over short but *finite* time intervals, e.g. years. The definition of $\text{E}$ here is the same except it is *defined on a grid of infinitesimal time intervals*, which discards the complexity of determining survival during finite intervals and makes this the [canonical](https://en.wikipedia.org/wiki/Canonical_form) definition.

For avoidance of doubt, I'll always use $\mathbb{E}$ to refer to true expectation.

///

On notation and terminology:

- The dataset $\mathscr{E}$ and, for $\text{E}$, the mortality $\mu$ are typically implicit from context -- it is rare that we need additional notation to make them explicit. But, for the avoidance of doubt, $\text{E}$ always implies a background mortality $\mu$.
- There is a multitude of notations for integrating using measures (see e.g. [here](https://math.stackexchange.com/questions/5230/is-there-any-difference-between-the-notations-int-fxd-mux-and-int-fx)), of which $\int \!f(x)\,\text{M}(\text{d}x)$ and $\int \!f\,\text{dM}$ are common. But the simplest is $\text{M}f$, which is what I'll use.

Although I've emphasised that $\text{A}$ and $\text{E}$ are measures over experience data, I confess that I don't use this terminology day-to-day[^LinearOp]. In fact, I've hardly heard anyone mention 'measures' in connection with experience analysis, which may explain why their importance seems to be overlooked and why some practitioners end up confused over the meaning of 'expected' (see box out), or whether individuals need to be tracked throughout the experience data.

[^LinearOp]: I think I usually describe $A$ and $E$ as 'linear operators'.

/// admonition | Insight 3. The continuous time definitions of A and E are canonical
    type: insight
    attrs: {id: "Insight3"}

The continuous time definitions of $A$ and $E$ are measures and *the* [canonical](https://en.wikipedia.org/wiki/Canonical_form) definitions of actual and expected deaths.

Other definitions can lead to confusion -- usually over $\text{E}$ vs true expectation -- and spurious complexity.

[[All mortality insights](/collated-mortality-insights#Insight3)]
///

## Why include&#xA0;*f*&#x202F;?

References to actual and expected deaths in mortality analyses are often written simply as $A$ and $E$, so why do we have a variable in our definitions?

A simple justification[^VariableAsTimeIndicator] is that real world mortality work requires *weighted* statistics[^Weighted]:

1. It is standard to analyse actual and expected deaths weighted by benefit amount ('amounts-weighted') as well as unweighted ('lives-weighted'). So including $f$ means that we have this requirement covered.

1. We may[^Relevance] want to weight data by its *relevance* (also known as reliability or importance).

The fundamental reason though is that, as noted above, *pretty much every useful aspect of mortality experience analysis can be expressed directly in terms of $\text{A}f$ and $\text{E}f$*.

/// admonition | Next article: [*A over E*](/2025-08/mortality-a-over-e/)
    type: tip

In [the next article](/2025-08/mortality-a-over-e/) I'll review the properties of $\text{A}f$ and $\text{E}f$ and their role in A/E analysis.

///


[^Weighted]: Note that $E$ can no longer serve as an estimate of its own variance when dealing with weighted statistics.

[^VariableAsTimeIndicator]: Another potential justification is that it is *mathematically convenient* to use $f\in \{0,1\}$ as an indicator of dataset membership *by time*. Unfortunately, this is an implementation nightmare in its full generality, and so has limited real-world value. There are better approaches to achieving this in practice.

[^Relevance]: And in a [future article](/2025-10/mortality-good-things-come-to-those-who-weight-i/#Def-relevance) *will*.

