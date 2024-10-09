---
draft: true 
date: 2024-10-09
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

# On contemporary mortality models for actuarial use

Stephen Richards' and Angus Macdonald's paper ['On Contemporary Mortality Models for Actuarial Use'](https://www.longevitas.co.uk/sites/default/files/2024-08/On_contemporary_mortality_models_for_actuarial_use.pdf) (due to be discussed at [the Institute of Actuaries on 24 October 2024](https://actuaries.org.uk/learn/events/events-calendar/sessional-meeting-contemporary-mortality-models-for-actuarial-use/)) makes the case for the following in mortality experience analysis[^Scope]:

[^Scope]: Although the paper's title suggests it covers all mortality modelling, its sole focus is analysis of portfolio mortality experience. For instance, mortality projection modelling is omitted entirely. This  doesn't detract from the paper's contents -- mortality experience analysis is an important topic in itself.

1. Use individual data if at all possible.

1. Work in continuous time and use instantaneous mortality rates, i.e. *μ* rather than *q*.

1. Consider mortality experience data as comprising a series of Bernoulli trials over infinitesimally small time periods.

The paper could be read as a polemic against actuaries who can't help but think in terms of *q* and whose first instinct is to group all time and age-dependent data on annual grids. *Which is fine by me -- I strongly agree with the thrust of the paper and, in particular, the above three points.*

So, having welcomed the paper, I do have a few observations...

<!-- more -->

### 1. Are actuaries really migrating from&#x00A0;*q*&#x00A0;to&#x00A0;*µ*?

The paper's introduction states that '*µ<sub>x</sub>* is usurping *q<sub>x</sub>* in actuarial work.' While I would like to think this is true, I can't help but wonder whether it's wishful thinking, or, if it is true, whether the rate of change is glacial.

!!! info inline end "Trying to switch the CMI Mortality Projections Model from *q* to *µ*[^CMIMNotMu]"

    As chair of the CMI's [Mortality Projections Committee](https://www.actuaries.org.uk/learn-and-develop/continuous-mortality-investigation/cmi-investigations/mortality-projections), I oversaw the revision of its Model in 2016. I was keen to remove all *q*-based aspects inherited from the previous approach, but -- relevant in this context -- all changes from *q* to *µ* were opposed by at least some actuaries. Despite objections[^CMIConsultation], the following two changes did gain majority support in consultation and made it into the final version:

    - Changing the 'long-term rate of mortality improvement' (LTR) from *q* to *µ*-based.

    - Changing the projections part of the model to work in directly in terms of *µ* rather than *q*.

    The only item remaining was to change the improvement factors output by the Model to be in terms of *µ*, but the committee itself felt this was a step too far.

[^CMIMNotMu]: Technically the model works in terms of *m*, not *µ*.

[^CMIConsultation]: See Q&#x202F;6.1 and Q&#x202F;6.2 in [WP&#x00A0;93](https://www.actuaries.org.uk/system/files/field/document/CMI%20WP093%20v02%202016-12-02%20-%20CMI%20Model%20consultation%20responses.pdf)
- In my experience, most actuarial *valuation* systems work with *q* and annual (or sometimes monthly) grids.

- Even within the mortality specialism, where, as the paper states, there are clear benefits to working in continuous time, I've encountered plenty of systems using a grid-based approach.

- Many actuaries simply prefer *q* in the same way that they also prefer *i* to [the force of interest](https://en.wikipedia.org/wiki/Compound_interest#Force_of_interest). See the break out box for a documented example.

### 2. 'Factorisation' of survival likelihood

In section 3.3, the paper notes that survival likelihood can be factorised over sub-time intervals and that multiple likelihoods for *contiguous* intervals can validly be multiplied together.

This is important, but I think there is a more general concept at play here, which is that the count of actual deaths, the integral of mortality rate over exposure periods and log-likelihood are *all [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)) over experience data[^LIsSignedMeasure]*. This means that they

[^LIsSignedMeasure]: Log-likelihood is a [*signed* measure](https://en.wikipedia.org/wiki/Signed_measure).

- give the same result when summed over a partitioned experience dataset *regardless of how it is partitioned*, and

- there is no need for experience time periods to be *contiguous* -- the sole requirement is that experience datasets do not intersect.

I think the above are two points are intuitively well understood by practitioners; my point is that the maths -- suitably defined -- aligns with this.

### 3. Likelihood vs log-likelihood

The paper focuses on likelihood, i.e. probabilities, but I'd argue that *log*-likelihood is both more *tractable* and more *fundamental*. These are intrinsically subjective concepts, so this is personal opinion, not a matter of right or wrong.

Let's take tractability first. The paper is littered[^ProdSigns] with product signs, i.e. $\prod$, arising from the use of probabilities. In contrast, working in term of log-likelihood results in standard integrals and makes linearity self-evident where present. I also didn't find the continued use of the Volterra product integral, i.e.

$$\prod_a^b\Big(1-f(t)\,\text{d}t\Big)=\exp\!\left(\!-\!\int_a^b \! f(t) \,\text{d}t \right)$$

enlightening. I'd suggest it's simpler to explain survival probabilities once, then take logs and work in terms of standard integrals, i.e.

$$\int_a^b \! f(t) \,\text{d}t$$

[^ProdSigns]: A quick search suggest there are over 60 product signs, i.e. $\prod$, in the paper.

Turning to fundamentality:

- A key rationale for fitting models by maximising log-likelihood and selecting between them using the AIC is [information theory](https://en.wikipedia.org/wiki/Entropy_(information_theory)), the core unit of which is [information content](https://en.wikipedia.org/wiki/Information_content), which in turn is defined as $\log p$ where $p$ is probability.

- The paper repeatedly refer to the concept of infinitesimal Bernoulli trials. I think this emphasis is correct, but note that the usual mathematical notation for infinitesimals is something like $\text{d} \ell$ and, for this to make sense, $\ell$ has to be log-likelihood.

- Observation&#x00A0;2 above (that there is a more general concept at play than just being able to factorise the survival likelihood over contiguous time periods) is best expressed using the language of [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)), which means using log-likelihood.
