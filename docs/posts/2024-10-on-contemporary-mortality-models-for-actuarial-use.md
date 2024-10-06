---
draft: true 
date: 2024-10-07
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

# On contemporary mortality models for actuarial use

Stephen Richards' and Angus Macdonald's paper ['On Contemporary Mortality Models for Actuarial Use'](https://www.longevitas.co.uk/sites/default/files/2024-08/On_contemporary_mortality_models_for_actuarial_use.pdf) (due to be discussed at [the Institute of Actuaries on 24 October 2024](https://actuaries.org.uk/learn/events/events-calendar/sessional-meeting-contemporary-mortality-models-for-actuarial-use/)) makes the case for the following in mortality experience analysis:

1. Use individual data and work in terms of continuous time and instantaneous mortality (i.e. *μ* rather than *q*).

1. Consider mortality experience data as comprising a series of Bernoulli trials over infinitesimally small time periods.
 
I strongly agree on both points.

That said, I think there is a potentially more intuitive approach than the one they present and one of the features they touch on is deserving of wider understanding.

<!-- more -->

The title suggests a wide gamut, but the paper's sole focus is the analysis of portfolio mortality experience. So, for instance, mortality projection models are omitted entirely. That's fine by me -- mortality experience analysis is an important topic in itself.

## The benefits of working in continuous time

Ironically, important to implement as integers to avoid the vagaries of floating point arithmetic. Floating point is neither *precise* -- the evaluation of *a*&#x202F;+&#x202F;*bc* depends on which machine instructions are used -- nor *associative* -- the result of (*a*&#x202F;+&#x202F;*b*&#x202F;)+*&#x202F;c* often differs from *a*&#x202F;+&#x202F;(*b*&#x202F;+*&#x202F;c*), sometimes with catastrophically so.

Nature of log-likelihood

Topics for another day:

- Locality and smoothness
- Weighted analysis


## The importance of measures

  [measures](https://en.wikipedia.org/wiki/Measure_(mathematics))
*multiplicative* representation of the mortality hazard rate they favour, and (a)&#x00A0;what they describe as 'factorization of survival model likelihoods' is deeply important, at both a practical and a theoretical level.

I've

Actuaries must model mortality to understand, manage and price risk. Continuous-time
methods offer considerable practical benefits to actuaries analysing portfolio mortality experience. This paper discusses six categories of advantage: (i) reflecting the reality of data produced
by everyday business practices, (ii) modelling rapid changes in risk, (iii) modelling time- and
duration-varying risk, (iv) competing risks , (v) data-quality checking and (vi) management information. Specific examples are given where continuous-time models are more useful in practice
than discrete-time models.

We reprise some common statistical models for actuarial mortality analysis using grouped
counts. We then discuss the benefits of building mortality models from the most elemental
items. This has two facets. First, models are better based on the mortality of individuals,
rather than groups. Second, models are better defined in continuous time, rather than over
fixed intervals like a year. We show how survival probabilities at the ‘macro’ level arise at
the ‘micro’ level from a series of Bernoulli trials over infinitesimally small time periods. Using
a multiplicative representation of the mortality hazard rate, we show how counting processes
naturally represent left-truncated and right-censored actuarial data, individual or age-grouped.
Together these explain the ‘pseudo-Poisson’ behaviour of survival model likelihoods.

 I couldn't agree more.

You can book here https://actuaries.org.uk/learn/events/events-calendar/sessional-meeting-contemporary-mortality-models-for-actuarial-use/

PART A

https://www.longevitas.co.uk/information-matrix-page/seriatim-data
https://www.longevitas.co.uk/information-matrix-page/fundamental-atom-mortality-modelling
https://www.longevitas.co.uk/information-matrix-page/product-integral-practice
https://www.longevitas.co.uk/information-matrix-page/when-your-poisson-model-not-poisson-model


https://actuaries.org.uk/media/q0gogxpw/on-contemporary-mortality-models-for-actuarial-use_practice.pdf

On Contemporary Mortality Models for Actuarial Use
https://www.longevitas.co.uk/sites/default/files/2024-08/On_contemporary_mortality_models_for_actuarial_use.pdf

Focussed on portfolio experience analysis -- projection is a big deal.

Conclusions:

10 Conclusions
Methods operating in continuous time, or in daily intervals, offer substantial advantages for actuaries.
Chief among these are the ability to reflect the reality of everyday business processes, and the ability
to handle rapid changes in risk level. In contrast, q-type models, especially those operating over a
one-year interval, make assumptions about the data that are hard to justify, and often require data
to be discarded if using standard GLMs. In addition, annual q-type models are unable to reflect
rapid intra-year movements in risk.
Continuous-time methods are also the more-practical solution to certain modelling problems. Examples include risks varying in more than one time dimension and modelling risks with competing
decrements. In contrast, annual q-type models within a GLM framework are not capable of simultaneously modelling time- and duration-varying risk. For competing risks, q-type models force the
pretence that the number of lives is smaller than it really is.
Continuous-time methods also offer useful data-quality checks. The visual nature of non- and
semi-parametric methods make them particularly useful for communication with non-specialists.
There are occasional circumstances when daily information is unavailable, or even deliberately
withheld, but actuaries should strive wherever possible to collect full dates and use continuous-time
methods of analysis.



# Thoughts on [Richards & Macdonald (2024)](https://www.longevitas.co.uk/sites/default/files/2024-08/On_contemporary_mortality_models_for_actuarial_use.pdf)

Good paper

Things right:

- Work in continuous time ('micro' in their terminology) -- I cannot commend this starting point (in fact I think they should have started with this)
- Individual ('seriatim') data beats grouped data
- Mortality model unit -- again, yes, I'd call it a data unit, although mixing probability masses and continuous functions is awkward analytically
- 'Pseudo-Poisson'
- Likelihood can be factorised -- this is important, although I think there is a better way to frame this

But I'd like to suggest an alternative and possibly more natural framework.

- Does not contradict Richards & Macdonald (!).
- Not new -- in use since 2009 or so. See e.g. [these 2011 slides](https://www.actuaries.org.uk/system/files/documents/pdf/tim-gordon.pdf).

Key concepts are

- E2R
- *Log*-likelihood is the canoncal measure (as opposed to likelihood)
- Express as A and E -- lots of good things follow from this formalism.

Key points

1. E2R algebra is well-defined on partitions, although the infinitessimal is awkward (there is no $\text{d}\varepsilon_t$)
1. A and E are *measures* over the data
    - This the factorisation point
    - This is practical point
        - A requirement to trace lives through all time does not need to be hard-coded -- real world example
        - We can process independent times slices of data in parallel (as well as independent lives)
        - We often have different overlapping data extracts
1. L is a simple function of A and E -- include overdispersion
1. E(A-E) and Var(A-E)
1. Proportional hazards is the canonical mortality model
1. Framework naturally can cope with weighted log-likelihoods -- can be seen as contentious
    - Everyone does this with $w\in \{0,1\}$
    - CMI has been doing for my lifetime and is still doing it -- the industry standard base tables are amounts-weighted
    - Useful to incorporate this upfront

You can do all of the above in performant computer code.

Criticisms

1. Why write $\prod_t(1-f_t \text{d}t)$ when you really want $-\int\! f_t\, \text{d}t$. It's *not* the item you want.
    - Maximum likelihood is always maximum log-likelihood in practice. 
    - This is not just because likelihood is numerically impractical to deal this; log-likelihood represents is information -- it's the key concept.
1. Missing a key point as to why 'factorisation' -- I'd call it partition -- is important.
1. Code is better when it maps to concepts -- E2R, A, E and log-likelihood are those concepts

Final note on locality of likelihood (well known in forecasting context) -- there is an implicit assumption of smoothness which is a topic for another day.

