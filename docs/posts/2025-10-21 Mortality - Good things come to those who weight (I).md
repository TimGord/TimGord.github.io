---
#draft: true 
date: 2025-10-21
authors:
  - timgord
#comments: true
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
#slug: add-custom-slug-here

# Don't forget to add the <!-- more --> separator!!!
---

# Mortality: Good things come to those who weight (I)

There was a time when actuaries understood that amounts-weighted statistics are the most appropriate ones for assessing DB pension plan liabilities. After all, 

- the objective is to value liabilities,
- liability is proportional to pension amount, and
- pension amount is a strong predictor of mortality,

 and so weighting the experience data by *what actually matters* seems sensible.

<!-- NB Does NOT show up in Markdown!! -->
<figure markdown="span">
  <figcaption">Distribution of individuals ('lives-weighted')</figcaption>
  ![Concentration](WeightLives.svg)
</figure>
<figure markdown="span">
  <figcaption">Distribution of pension amount ('amounts-weighted')</figcaption>
  ![Concentration](WeightAmounts.svg)
</figure>

But then some actuaries noticed that statistics textbooks do not mention weighting by amounts, and so they decided that it must be wrong.

<!-- more -->
--8<-- "snippets/mortality-series-list.md"

At least I think this must have happened on occasion, because I've seen some horrors in my time where actuaries discarded simple amounts-weighted A/E in favour of lives-weighted models but, in doing so, *made their modelling worse*, e.g. by introducing systematic bias.

The is the first of three articles explaining why using weighted statistics makes sense in the context of DB pension plan mortality analysis. In this article, I'll define the objective, take stock of what we already know and introduce *relevance*. In the second article, I'll set out the maths and in the third consider the practical applications.

### *1. What's the objective?*

The objective is *to determine the best estimate and uncertainty of **the present value[^CashflowsToo] of liabilities**.* Along the way, yes, we will need to generate and test mortality models, but we do this because it enables us to meet the objective, not as an end in itself.

[^CashflowsToo]: In the real world, we'd probably also want projected cashflows, but let's keep things simple.

Let me put this another way. If

- $L$ is log-likelihood,
- $V$ is present value of liabilities, and
- $\beta$ is a vector of the model parameters

then the mechanism that ultimately determines how much $\beta$ matters is $\partial V/\partial \beta$, not $\partial L/\partial \beta$[^Pathway].

[^Pathway]: Of course, $\partial L/\partial \beta$ (and $\partial L^2/\partial \beta^2$) may lie on the pathway to determining $\partial V/\partial \beta$.

At its most extreme, if you have a parameter $\beta$ for which $\partial V/\partial \beta=0$ then you don't care how well $\beta$ fits the data.

### *2. What do we already know?*

If your domain is one in which very little is actually known then exploratory modelling will likely be a large part of the task of estimating the liabilities.

But, when it comes to assessing mortality for a specific DB pension plan, a lot has already been determined:

- Historical mortality improvements are usually given, so we don't need to fit those[^HistoricalMortalityImprovements].
- We have a good *a priori* understanding of the likely range of base mortality -- we may even have a literal prior mortality model (typically using postcodes etc).
- We know that pensioner mortality varies relatively smoothly by socio-economic type and that variation can be captured on a monotonic one dimensional scale (see [previous article](/2025-10/mortality-pensioner-mortality-variation/)).

[^HistoricalMortalityImprovements]: Linking historical mortality improvements to projected future improvements requires experience data for *much* longer time periods and wider age ranges than are available for even the very largest pension plans, so this analysis is usually independent. Yes, there is often an attempt to map the socio-economic profile of an individual plan to a mortality projection, but this mapping is itself also usually determined *a priori*.

What this means is that modelling base mortality for DB pensioners is not so much starting from scratch but more a matter of fine-tuning models we already have to hand.

### *3. Defining and incorporating data relevance*

Actuaries make decisions on the *relevance* of mortality experience data all the time.

- It is routine to exclude older data over concerns that the population composition may have changed, that historical values of covariates cannot reliably be compared with current values or simply that mortality is time-heterogeneous.
- The CMI excludes data over 41 years old from its projections model[^CMIModel41Years], and, more recently, it has under-weighted data for years materially affected by COVID-19.
- The mortality experience for pensioners below age 65 in DB pension plans is usually excluded because it is distorted by the heavier mortality of ill health early retirees.
- High age (e.g. over age 95) mortality experience is routinely excluded because of concerns over rating factor validity and because models can be sensitive to unnotified deaths at high ages[^HighAgeConvergence].

[^CMIModel41Years]: This results in the questionable effect that if an atypical calendar year falls off the *start* of the 41 year data period then this dropping of shortly-to-be-out-of-date data can impact the CMI Model's *current* predictions.

[^HighAgeConvergence]: Survival is unlikely at very high ages, which means an unnotified death look statistically significant. Models typically also assume mortality convergence at very high ages, which amplifies the effect further still.

What's worthy of note is how very fuzzy, uncertain and judgement-based the include / exclude decision is in contrast to the maths underlying most mortality modelling, which is sharp-edged, treating data as either 100% relevant or 100% irrelevant[^AllOrNothing]. 

[^AllOrNothing]: For the avoidance of doubt, I am *not* suggesting that using a well-defined framework to analyse data and create models is unreasonable, not least because it helps define and corral the fuzziness. But if (a)&#xA0;it's not clear which data to include or exclude and (b)&#xA0;inclusion / exclusion has a material impact on the results, then this in itself implies that something is missing from the modelling.

What happens if instead we try to incorporate relevance into the modelling itself? The fuzziness is not going to go away; all we're doing is moving fuzziness already present in the real world to inside our modelling. But this approach may provide us with insights, so let's give it a go.

First, we need to define it: a <span id="Def-relevance">**relevance**</span>[^RelevanceOtherNames] $r_{itku} \in [0,1]$ is the multiplicative factor to apply to the log-likelihood of the experience data for individual $i$ at time $t$ to assess liabilities for individual $k$ as at time $u$[^RelevanceProperties].

[^RelevanceOtherNames]: Relevance is also known as *importance* or *reliability*. Beware that these words are also used to described other types of weights, so it's important to check definitions. And sometimes weighting log-likelihood is assumed to be such an obvious concept that it is not given a special name at all.

[^RelevanceProperties]: For consistency, a relevance should also be 

    - 100% self-relevant, i.e. $r_{aa}=1$, and
    - symmetric, i.e. $r_{ab}= r_{ba}$,
    
    where I've written $a$ for $it$ and $b$ for $ju$.

I don't think this is contentious -- relevance is not uncommon in modern statistical sampling. And I suspect that you didn't bat an eyelid when I mentioned above that the CMI under-weighted COVID-19 affected data in its Mortality Projections Model, and yet those weights are nothing more than relevances.

We're interested in the relevance of mortality experience data when assessing pension plan liabilities. An obvious example, already alluded to, is that older data is typically less relevant than more recent data. A relevance-based framework can certainly handle that, but the specific type of relevance that I want to consider is the relevance of one individual's mortality experience to the valuation of a liability relating to another individual.

To make progress we'll need to see some maths, which I'll set out in my next article.

## A litmus test

In the meantime, I'll pose a simple question: *if you were forced to choose between A/E on a lives or amounts-weighted basis as the sole available metric for assessing the liabilities of a typical UK DB pension plan which would you choose*?

/// admonition | Scenario
    type: example

To make this more concrete, imagine a portfolio comprising two groups as follows:

|Group|Lives|Total liability|Average liability|Proportion by lives|Proportion by liability|
|:--:|--:|--:|--:|--:|--:|
|A|1,500|£100m|£67k|75%|25%|
|B|500|£300m|£600k|25%|75%|

Would you value the liabilities  for group&#x00A0;B, i.e. 75% of the liabilities, using unweighted data, i.e. 75% based on group&#x00A0;A?

///

Neither is ideal, but only one of these options gets you a shot at achieving the objective, i.e. valuing the liabilities. The other may be a right answer but to a different question.

/// admonition | Rico Mariani's pit of success
    type: info
    attrs: {class: "inline end"}

Rico Mariani's [2003 *ad lib*](https://ricomariani.medium.com/pit-of-success-a-2003-ad-lib-ffb05ccdd2c6) expressed the design principle that *users should end up with a good answer by default*. The context was the design of the then new .NET coding framework and its associated C# language. The principle was reiterated by, amongst others, [Jeff Atwood](https://blog.codinghorror.com/falling-into-the-pit-of-success/) and [Eric Lippert](https://ericlippert.com/2007/08/14/c-and-the-pit-of-despair/), which are well worth a read, even if you're not a coder.

I suggest that it's a general design principle. To quote Mariani:

> Success has to be like falling into a big pit. You can’t help but win. You try to do it wrong, but nope, you fell in *The Pit of Success*. You win again!

///

<span id="Ref-standardised-processes">
If you're thinking that being forced to use a single metric is unrealistic then think again. Standardised processes that have to handle 50, 100 or more pension plan mortality analyses per year robustly and efficiently often collapse pension plan mortality experience down to one variable when mixing a pension plan's own mortality experience with the mortality predicted by a prior model.
</span>

And, beyond that, there's an important principle at stake here: a minimum target for any modelling framework is that it should *provide a sensible answer by default*. In a very different context, Rico Mariani named this principle the 'pit of success' -- see box out.

I suggest that the equivalent in the context of DB pension plan mortality modelling is that a well-designed framework applied to a single parameter model should *default to* giving a reasonable estimate of mortality for *valuing liabilities*.

For avoidance of doubt, I am *not* claiming that this excludes either the adoption of more complex models (commensurate with the amount of experience data) or a role for expert judgement -- both are essential parts of the mortality modelling toolkit. But if the framework you're using does not *default to* giving a reasonable estimate of mortality for *valuing liabilities* in the simplest scenario then I think you have some questions to answer.

/// admonition | Next article: *Good things come to those who weight (II)*
    type: tip
<!--
/// admonition | Next article: [*Good things come to those who weight (II)*](/2025-10/mortality-good-things-come-to-those-who-weight-ii/)
    type: tip
-->

In part II, <!-- [part II](/2025-10/mortality-good-things-come-to-those-who-weight-ii/), --> I'll set out some maths to make the above more concrete.

And then in part III, <!-- [part III](/2025-10/mortality-good-things-come-to-those-who-weight-iii/), --> the final article in this series, I'll address its practical application.

///

