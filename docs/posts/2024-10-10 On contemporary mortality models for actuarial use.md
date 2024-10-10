---
#draft: true 
date: 2024-10-10
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

[^Scope]: The paper's title suggests it covers all mortality modelling, but its sole focus is the analysis of portfolio mortality experience. So, for instance, mortality projection modelling is omitted entirely. This doesn't detract from the paper's contents -- mortality experience analysis is an important topic in itself.

1. Use individual data if at all possible.

1. Work in continuous time and use instantaneous mortality rates, i.e. *μ* rather than *q*.

1. Consider mortality experience data as comprising a series of Bernoulli trials over infinitesimally small time periods.

The paper could be read as a polemic against actuaries who can't help but think in terms of *q* and whose first instinct is to group all time and age-dependent data on annual grids. *Which is fine by me -- I agree with the thrust of the paper and, in particular, the above three points.*

So, having welcomed the paper, I do have a few observations...

<!-- more -->

## Observations

### 1. Are actuaries really migrating from&#x00A0;*q*&#x00A0;to&#x00A0;*µ*?

The paper's introduction states that '*µ<sub>x</sub>* is usurping *q<sub>x</sub>* in actuarial work.' While I wish this were true, I can't help but wonder whether it's wishful thinking, or, if it is true, whether the rate of change is glacial.

!!! info inline end "Trying to switch the CMI Mortality Projections Model from *q* to *µ*[^CMIMNotMu]"

    [^CMIMNotMu]: Technically the model works in terms of *m*, not *µ*.

    As chair of the CMI's [Mortality Projections Committee](https://www.actuaries.org.uk/learn-and-develop/continuous-mortality-investigation/cmi-investigations/mortality-projections), I oversaw the revision of its Projections Model in 2016. I was keen to remove all *q*-based aspects inherited from the previous approach, but -- relevant in this context -- all changes from *q* to *µ* were opposed by at least some actuaries. 
    
    Despite objections[^CMIConsultation], the following two changes did gain majority support in consultation and made it into the final version:

    - Changing the 'long-term rate of mortality improvement' (LTR) from *q* to *µ*-based.

    - Changing the projections part of the model to work in directly in terms of *µ* rather than *q*.

    The only item remaining was to change the improvement factors output by the Model to be in terms of *µ*, but the committee itself felt this was a step too far.

    [^CMIConsultation]: See Q&#x202F;6.1 and Q&#x202F;6.2 in [WP&#x00A0;93](https://www.actuaries.org.uk/system/files/field/document/CMI%20WP093%20v02%202016-12-02%20-%20CMI%20Model%20consultation%20responses.pdf)

- Many actuaries prefer *q* in the same way that they prefer annually-compounded interest rates to [the force of interest](https://en.wikipedia.org/wiki/Compound_interest#Force_of_interest). See the break out box for a documented example.

- Like it or not, *q* is the current *lingua franca* in the sense that '110% of S4PxA' without further qualification means scale *q* by 110%[^QOver1].

    [^QOver1]: And gloss over how to treat ages for which $q_x\ge1$ !

- In my experience, most actuarial *valuation* systems work with *q*, data grouped by age and annual time grids[^QForValuation].

    [^QForValuation]: There are valid practical arguments in favour of this approach.

- Even within specialised mortality experience analysis environments, where, as the paper notes, there are clear benefits to working in continuous time, I've encountered plenty of systems that use *q*.

### 2. 'Factorisation' of survival likelihood

In section&#x00A0;3.3 of part&#x00A0;II, the paper notes that survival likelihood can be factorised over sub-time intervals and that multiple likelihoods for *contiguous* intervals can validly be multiplied together.

I think there is a more general concept at play here, which is that (a)&#x00A0;the count of actual deaths, (b)&#x00A0;the sum of integrals of mortality rate over exposure periods and (c)&#x00A0;log-likelihood are *all [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)) over experience data[^LIsSignedMeasure]*. This means that they

[^LIsSignedMeasure]: Log-likelihood is a [*signed* measure](https://en.wikipedia.org/wiki/Signed_measure).

- give the same result when summed over a partitioned experience dataset *regardless of how it is partitioned*, and

- in particular, when summing over multiple experience datasets, there is no need for experience time periods to be *contiguous* -- the sole requirement is that experience datasets do not intersect.

I think the above are two points are intuitively well understood by practitioners; my point is that the maths -- suitably defined -- aligns with this.

### 3. Likelihood vs log-likelihood

The paper focuses on likelihood, i.e. probability, but I'd argue that *log*-likelihood is both more *tractable* and more *fundamental*. These are subjective concepts, so this is personal opinion, not a matter of right or wrong[^Napier].

[^Napier]: That said, I wonder whether [Napier](https://en.wikipedia.org/wiki/Mirifici_Logarithmorum_Canonis_Descriptio) is rolling in his grave over Part&#x00A0;II of the paper.

Let's take tractability first.

- The paper is littered[^ProdSigns] with product signs, i.e. $\prod$, arising from the use of probabilities. In contrast, working in term of log-likelihood results in standard integrals and makes linearity self-evident where present. As a case in point, I didn't find the continued use of the [Volterra product integral](https://en.wikipedia.org/wiki/Product_integral#Type_I:_Volterra_integral), i.e.

    $$\prod_a^b\Big(1-f(t)\,\text{d}t\Big)$$

    enlightening. I'd suggest it's simpler to explain survival probabilities once, then take logs and work in terms of standard integrals, i.e.

    $$-\int_a^b \! f(t) \,\text{d}t$$

    [^ProdSigns]: A quick search suggests there are over 60 product signs, i.e. $\prod$, in the paper.

- Implementing mortality experience analysis solely using probability fails because it runs straight into numerical underflow. So if you believe that code should map to concepts, the concept you want is log-likelihood.

Turning to fundamentality:

- A key rationale for fitting models by maximising log-likelihood and selecting between them using the AIC is [information theory](https://en.wikipedia.org/wiki/Entropy_(information_theory)), the core unit of which is [information content](https://en.wikipedia.org/wiki/Information_content#Definition), which in turn is defined as −log&#x202F;*p* where *p* is probability.

- The paper repeatedly refers to the concept of infinitesimal Bernoulli trials. I couldn't agree more, but the usual mathematical treatment of summed infinitesimals requires something akin to dL to make sense, which works only if L is log-likelihood.

- Observation&#x00A0;2 above (that there is a more general concept at play than just being able to factorise the survival probabilities over contiguous time periods) is best expressed using the language of [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)), which means using log-likelihood.

## Conclusion

Stephen Richards' and Angus Macdonald's [paper](https://www.longevitas.co.uk/sites/default/files/2024-08/On_contemporary_mortality_models_for_actuarial_use.pdf) is to be welcomed for making the case for using continuous time and instantaneous rates in mortality experience analysis.

If it makes even one actuary think before diving in with a *q*-based approach, then that's a good thing.
