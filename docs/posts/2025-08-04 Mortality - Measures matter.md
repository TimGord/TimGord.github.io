---
#draft: true 
date: 2025-08-04
authors:
  - timgord
categories:
  #- Coding
  #- Forecasting
  - Maths & stats
  - Mortality & longevity
  #- Presentation

#comments: true

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

This is the first in a series of articles outlining *mortality experience analysis fundamentals*, i.e. estimating  the mortality applying to a defined group of individuals (e.g. members of a pension plan) assuming that there is a *fixed* underlying rate of mortality.

This will be fairly technical, but I'll aim

- to be concise,
- to pull out the key insights, and
- to map concepts one-to-one to the process of actually carrying out a mortality experience analysis or calibrating and selecting mortality models.

For the most part, what I'll cover is reasonably well known, but not always available in one place or easily accessible. 
It won't be all plain sailing, and there'll be a few potentially contentious or even surprising points along the way.

In this first article I'll set out the foundations on which we will build.

<!-- more -->

--8<-- "snippets/mortality-series-list.md"

## Definitions

An <span id="Def-E2R">**exposed-to-risk**</span> (E2R) for an individual comprises

- an exposure period[^ContinuousTime] $[\nu,\tau)$ throughout which the individual was alive, and
- an indicator $\delta$, which is $1$ if the individual died at $\tau$ or else $0$.

I'll write an E2R as $\varepsilon=(\nu,\tau,\delta)$.

For each individual we also have a set of <span id="Def-fact">**facts**</span>, $i$, known at the time of the analysis and which are time invariant.

If a fact relates to something that would have happened except that the individual died, e.g. pension in payment in 2025 for an individual who died in 2024, then we assume this fact is determined *as if the individual had lived*. This is critical for unbiased analysis and is sometimes referred to as 'the principle of correspondence'[^NoCheating].

[^NoCheating]: From a technical point of view, this principle also means mortality models can't cheat simply by looking at the data.

An <span id="Def-exp-data">**experience dataset**</span> $\mathscr{E}$ comprises pairs of facts and E2Rs, i.e. $\{(i,\varepsilon)\}$ for which no E2Rs for the same individual overlap in time[^Deduplication].

A <span id="Def-variable">**variable**</span> is a real-valued function $f(i,t)$ of facts $i$ and time $t$ that is left-continuous with right limits[^LeftContinuity] in $t$. There are a few points worth noting:

- A variable as defined here is *not* random -- by assumption, the sole source of stochasticity is whether individuals die or not, which is embedded in $\varepsilon$.

- The continuity condition allows theoretical generality, but is deeply impractical for actual implementation, for which a more realistic requirement is that variables are *smooth*[^NotFullyDefined] in $t$ at the scale of numerical integration.

A <span id="Def-mortality">**mortality**</span> $\mu$ is a strictly positive variable[^Terminal] that specifies the probability of an individual dying over an infinitessimal time interval $\text{d}t$ as $\mu(i,t)\,\text{d}t$, i.e. an independent[^Independence] [Bernoulli trial](https://en.wikipedia.org/wiki/Bernoulli_trial).

[^ContinuousTime]: I'll take it as a given that we should work in (some representation of) continuous time if at all possible. Otherwise we'd be (a)&#xA0;throwing away data and (b)&#xA0;creating additional cognitive load and potentially biased or even plain wrong results by having to make assumptions about averages.

[^Deduplication]: This is easy to stipulate in theory, but data de-duplication is typically an essential and non-trivial part of real world mortality experience analysis.

[^LeftContinuity]: Left continuity is required so that the value at death is consistent with the value over the immediately preceding exposure.

[^NotFullyDefined]: We'll leave *smoothness** not fully defined for now, but a minimum requirement is variables are [absolutely continuous](https://en.wikipedia.org/wiki/Absolute_continuity) in $t$.

[^Terminal]: An implementation would also need a mortality to specify a terminal date or age by individual (because mortality tables stop), but I don't need that for this exposition.

[^Independence]: For the avoidance of doubt, we assume that these Bernoulli trials are independent by time and by individual.

## What is random?

We will assume that the mortality $\mu$ at hand is itself completely deterministic and the sole source of random variation is whether or not individuals die according to $\mu$.

In practice, *this is never true for mortality data* because $\mu$ itself is also stochastic. <span id="Def-overdispersion">This manifests as observed variances being materially higher than would be predicted by a fitted Poisson distribution, and is known as **overdispersion**.</span>

/// admonition | Insight 1. Always allow for overdispersion
    type: insight
    attrs: {id: "Insight1"}
If you don't allow for overdispersion then you *will* underestimate uncertainty and overfit models.

[[All mortality insights](/collated-mortality-insights/#Insight1)]
///

The good news is that there is a standard way of adjusting for overdispersion (which we'll do this later in this series). For the time being, whenever you come across references to mortality variance or uncertainty (in this blog or anywhere else), there should have a little voice in your head saying '*remember to allow for overdispersion*'.

## The measures that matter

An experience dataset is [measurable](https://en.wikipedia.org/wiki/Measurable_space)[^Measurable] in the mathematical sense, which means we can define sums (or integrals) of a variable over the data and it doesn't matter how we partition the data to get the sum -- we'll always get the same answer[^Parallel].

[^Measurable]: Experience data is trivially measurable because countable sets (i.e. the individuals) are measurable and the real line (i.e. the non-overlapping exposure periods for each individual) is measurable.

[^Parallel]: The freedom to partition experience data may also present opportunities to run [calculations in parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel). (I suggest that your bulk mortality experience calculations should be running in parallel at least somewhere along the chain.)

My intuition is to consider experience data as comprising infinitesimals $\text{d}(i,\varepsilon)$ over which we can integrate.

/// admonition | Insight 2. Experience data is '[measurable](https://en.wikipedia.org/wiki/Measurable_space)'
    type: insight
    attrs: {id: "Insight2"}

Provided we use [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)), we'll always get the same answer *regardless of how an experience dataset is partitioned*.

In particular, there is no need

- *for experience time periods to be contiguous*[^OmitPandemic] -- the sole requirement is that elements of the experience datasets do not intersect, or
   - *to track individuals across experience datasets relating to different time periods*[^Tracking].

[^OmitPandemic]: An obvious example is excluding mortality experience from the height of the COVID-19 pandemic, potentially resulting in *non-contiguous* data from before and after the excluded time period.

[^Tracking]: Tracking individuals across experience datasets for different time periods may however be a very sensible data check.

[[All mortality insights](/collated-mortality-insights/#Insight2)]
///

But which measures should we use?

There are essentially two core measures. You almost certainly have already come across them and, as we'll cover in this series, *pretty much every aspect of mortality experience analysis can be expressed directly in terms of them*:

<span id="Def-AE-ops">

1. **Actual deaths** is the sum of $f$ over deaths at time of death:

    $$\text{A}f=\sum_{(i,\varepsilon)\in \mathscr{E}}\delta_\varepsilon f(i,\tau_\varepsilon)$$

    Note that the variable, $f$, is evaluated at time of death.

1. **Expected deaths**[^ExposureMeasure] with respect to mortality $\mu$ is the integral of $\mu$ times $f$ over all exposure periods:

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

$\text{E}f$ is a *random variable* (like $\text{A}f$), and so describing it as 'expected' deaths unsurprisingly repeatedly gives rise to confusion. But the practice is so ensconced that using a different term would be even more confusing.

The terminology arises because, for typical mortality rates, $\text{E}f$ is an *approximation* to true expectation of deaths over small timescales (e.g. a year). But, at an infinitesimal scale, they are *identical*  which is why using measures and $\text{E}$ as defined here are [*canonical*](https://en.wikipedia.org/wiki/Canonical_form).

For avoidance of doubt, I'll always use $\mathbb{E}$ to refer to true expectation.

///

On notation and terminology:

- The dataset $\mathscr{E}$ and, for $\text{E}$, the mortality $\mu$ are typically implicit from context -- it is rare that we need additional notation to make them explicit.
- There is a multitude of notations for integrating using measures (see e.g. [here](https://math.stackexchange.com/questions/5230/is-there-any-difference-between-the-notations-int-fxd-mux-and-int-fx)), of which $\int \!f(x)\,\text{M}(\text{d}x)$ and $\int \!f\,\text{dM}$ are common. But the simplest is $\text{M}f$, which is what we'll use.

Although I've emphasised that $\text{A}$ and $\text{E}$ are measures over experience data, I confess that I don't use this terminology day-to-day[^LinearOp]. In fact, I've hardly heard anyone mention 'measures' in connection with experience analysis, which may explain why their importance seems to be regularly overlooked, sometimes leading practitioners down blind alleys in relation to e.g. the meaning of 'expected' (see box out) or whether experience data needs to be contiguous (which has cropped up more than once in multi-£ billion transactions I have worked on).

[^LinearOp]: I think I usually describe $A$ and $E$ as 'linear operators'.

/// admonition | Insight 3. The continuous time definitions of A and E are canonical
    type: insight
    attrs: {id: "Insight3"}

The continuous time definitions of $A$ and $E$ are measures and *the* [canonical](https://en.wikipedia.org/wiki/Canonical_form) definitions of actual and expected deaths.

Other definitions can lead to confusion -- usually over $\text{E}$ vs true expectation -- and spurious complexity.

[[All mortality insights](/collated-mortality-insights/#Insight3)]
///

## Why include&#xA0;*f*&#x202F;?

References to actual and expected deaths in mortality analyses are not usually accompanied by a reference to a 'variable' and are usually written simply as $A$ and $E$, so why do we have one in our definitions?

The immediate justification[^FAsIndicator] is that real world mortality work requires *weighted* statistics:

1. It is standard to analyse actual and expected deaths weighted[^NextArticle] e.g. by benefit amount as well as unweighted ('lives-weighted' in actuarial parlance). So including $f$ means that we have this requirement covered.

1. We may want to weight data by its *relevance* (also known as reliability or importance), in which case we'd use $0\le f\le 1$. (This too will be covered in a future article.)

And once we incorporate weighted statistics then $E$ can no longer serve as an estimate of its own variance and so this alone means we need something more general.

The real reason though is that, as noted above and as we’ll cover in the articles in this series, *pretty much every aspect of mortality experience analysis can be expressed directly in terms of $\text{A}f$ and $\text{E}f$*.

[^FAsIndicator]: It can be *mathematically convenient* to use $f\in \{0,1\}$ as an indicator of dataset membership *by time*. But this is an implementation nightmare, and so has limited practical value. There are much better approaches to achieving this in real-world implementations.

[^NextArticle]: This is the topic of [the next article](/2025-08/mortality-a-over-e/) in this series.