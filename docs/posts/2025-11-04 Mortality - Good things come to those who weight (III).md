---
#draft: true 
date: 2025-11-04
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

# Mortality: Good things come to those who weight (III)

This is the final article of three on *weighted mortality analysis*.

If you haven't already read [Part&#xA0;I](/2025-10/mortality-good-things-come-to-those-who-weight-i) then I recommend you do that first.

And if you're really keen to understand the maths then give [Part&#xA0;II](/2025-10/mortality-good-things-come-to-those-who-weight-ii) a go, but it's not essential.

In this article, I'll review the implications and provide some practical recommendations.

<!-- more -->
--8<-- "snippets/mortality-series-list.md"

## Relevance revisited

But first, let's revisit [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance).

Statistics text books typically assume not only that experience data is either 100% relevant or 100% irrelevant, but that determining whether data is relevant or irrelevant is obvious. And in many cases this may be reasonable -- if we are testing the reliability[^HumanReliability] of widgets then get some widgets and test them, which sounds well-defined[^TestingFails].

[^HumanReliability]: It is a linguistic convention that we use 'reliability' in an engineering context and 'mortality' in a biological context.

[^TestingFails]: It isn't -- try Googling the testing of Challenger Space Shuttle o-rings, Volkswagen diesel emissions or Grenfell Tower cladding.

But what experience data is relevant for estimating the mortality for a current or future pensioner in the XYZ Pension Plan? There are two dimensions, time and individual (which are non-exclusive, you can do one or both).

### *Time-based relevance*

The notion that *relevance typically decreases as time elapses* is uncontentious, at least conceptually:

- This doesn't imply that the most recent data is *always* the most relevant. For instance, we may also decide to treat COVID-impacted years as less relevant.

- Smoothly varying relevance by time is not usually a built in feature. Instead it is usually left as an *ad hoc* practitioner decision, and typically implemented on an all-or-nothing basis.

An example of a time-based relevance[^RelevanceConstruction] is

$$r_s^t=\exp\!\big(\!-\phi\,\big|t-s\big|\big)$$

where $s$ and $t$ are dates (measured in years) and $\phi>0$.

[^RelevanceConstruction]: If $d(a,b)$ is a [metric](https://en.wikipedia.org/wiki/Metric_space#Definition) then $r_a^b= \exp\{-d(a,b)\}$ is a [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance). And the simplest metric is $\big|b-a\big|$.

It is a special case if relevance is purely time-based, i.e. no variation by individual, *because we can shortcut all the above simply by scaling the data by relevance* and proceeding with the lives-weighted approach. While, yes, this is a relevance-based approach, the maths is so trivial that it is usually treated as obvious and goes unremarked[^CmiModelRelevance].

[^CmiModelRelevance]: For instance, this is how the CMI adjusted its Mortality Projections Model to cope with COVID-affected mortality experience. I note that the debate then was essentially over *the degree of relevance*, not whether adjusting for relevance was wrong.

This is applicable beyond base mortality -- I'd recommend using time-based relevance for calibration of *all* mortality projection and stochastic mortality models (including the CMI Mortality Projections Mode).

/// admonition | Insight 17. Always allow for time-based relevance
    type: insight
    attrs: {id: "Insight17"}

Allowing for time-based relevance, e.g. using 

$$r_s^t=\exp\!\big(\!-\phi\,\big|t-s\big|\big)$$

where $s$ and $t$ are dates (measured in years) and $\phi>0$, is to be preferred in *all mortality modelling contexts* because

- it automatically allows for the decay in relevance as time elapses, and
- compared with fixed windows, leaves models less sensitive to the falling away of more historical data.

If relevance is purely time-based then this can be accomplished simply by scaling the experience data.

[[All mortality insights](/collated-mortality-insights#Insight17)]

///

### *Individual-based relevance*

The more interesting question is *relevance by individual*.

I suggest that

- it's unreasonable to assume that the mortality experience of all individuals in the same pension plan is 100% relevant[^AllHumansOver65], and

- relevance should take account of pension given that it's the one simple [fact](/2025-08/mortality-measures-matter/#Def-fact) that is strongly correlated with both (a)&#xA0;mortality and (b)&#xA0;liability impact.

[^AllHumansOver65]: In the same way that it would be unreasonable to assume that the mortality experience of all [800&#xA0;million](https://ourworldindata.org/grapher/population-by-age-group) humans over age&#xA0;65 is 100% relevant -- nominative determinism is not statistics.

If you have a lot of experience data, then you will likely end up sub-dividing your model by pension band (or equivalent), in which this probably[^LivesCriterion] doesn't matter.

[^LivesCriterion]: Model *selection* (e.g. using the [AIC](/2025-08/mortality-suddenly-aic/#Def-Var-LP)) will still be affected because it would be determined by lives affected, not liability impact, although I suspect that the impact in most cases is second order.

But if you don't have a lot of data (or if you calibrate any covariates across all lives) then *not taking account of relevance will have an impact*. A particularly fertile area is processes that handle lots of cases each of which has relatively small amounts of experience data.

Given that the underlying variables feeding into data relevance need to be generally available, simple and robust, an obvious[^Dimensionless] candidate for individual relevance is log-pension, for example[^RelevanceConstruction]

[^Dimensionless]: Obvious because (a)&#xA0;results should be independent of units, (b)&#xA0;ratios are the simplest dimensionless means of comparison and (c)&#xA0;measuring distance requires taking differences. Hence use logarithms.

$$\begin{aligned}
r_p^q
&=\exp\!\big(\!-\psi\,\big|\log q-\log p\big|\big)
\\[1em]
&= \min\!\big(p/q,q/p\big)^\psi
\end{aligned}$$

where $p$ and $q$ are pension amounts (revalued to the same date for consistency) and $\psi>0$.

## Behaviour in a simple case

In order to help us intuit what's going on, we can ask what happens if relevance is always 0 or 1? In this case, relevance *partitions*[^RelevancePartition] the experience data and valuation data into a finite number of groups labelled $G$[^PartitionLabel], within each of which all experience is 100% relevant to all valuation individuals. 

[^RelevancePartition]: This follows from the properties of [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance), i.e.

    - 100% self-relevance, i.e. $r_{it}^{it}=1$,
    - symmetry, i.e. $r_{it}^{ju}= r_{ju}^{it}$, and
    - consistency, i.e. $r_{it}^{kv} \ge r_{it}^{ju} \cdot r_{ju}^{kv}$.

[^PartitionLabel]: Because this is a partition, we can use the same label for experience and valuation data.

The relevance approach collapses to[^MathsNotShown] 

$$\begin{aligned}
\hat\beta &\approx \big(V'\big)^{-1}\sum_G V'_G \, \hat\beta_G
\\[1em]
\text{Var}\big(\hat\beta\big) &\approx \big(V'\big)^{-2}\sum_G \big(V'_G\big)^2 I_G^{-1}
\end{aligned}$$

[^MathsNotShown]: I haven't shown the maths, but this follows direct from the equations in Insights [15](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight15) and [16](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight16).

where

- $V_G$ is the sum of liability values for individuals in group $G$, with the total being $V=\sum_G V_G$,
- $L_G$ is the sum of log-likelihood for the E2Rs in group $G$, with the [relevant information](/2025-10/mortality-good-things-come-to-those-who-weight-ii/#Def-Ik) for group $G$ being $I_G=-L''_G$, and
- $'$ and $''$ are first and second derivatives with respect to the mortality model parameter $\beta$.

In other words, 

- the *overall best estimate* is the sum of the per-group best estimates *weighted by liability impact* ($V'_G$), and

- the *variance* of the overall best estimate reflects both (a)&#xA0;liability impact ($V'_G$) and (b)&#xA0;the amount of experience data available ($I_G$) per group.

This is self-evidently the best estimate for liability value and its uncertainty given the assumptions and so it is reassuring that the general approach gives the correct answer in this case.

## Application

In [Part&#xA0;II](/2025-10/mortality-good-things-come-to-those-who-weight-ii), we showed that if

- a mortality model has a single scalar parameter, and
- we are provided with a [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance) of the log-likelihood of the E2R for individual $i$ at time $t$ to individual $j$ in the valuation data as at the valuation date $t_0$,

/// admonition | It's not amounts-weighted A/E vs lives-weighted modelling
    type: info
    attrs: {class: "inline end"}

One reason the 'lives-weighted is the only valid approach' view has propagated is because some actuaries think the only combinations of options are

- amounts-weighted A/E, or
- sophisticated lives-weighted modelling,

when actually the choices of weight and modelling approach are orthogonal.

///

then we can define a weight such that maximising the *weighted* log-likelihood *automatically* results in the best estimate of the present value of liabilities when using that single parameter mortality model.

I suggest that it is best practice to use an approach that

- takes explicit account of liability impact (given that assessing liabilities is our objective), and

- in particular, defaults to unbiased liability valuation results *regardless of the quantum of experience data available* (which is *not* automatically the case for lives-weighted statistics).

Using a weighted log-likelihood as derived to fit DB pensioner base mortality models meets these criteria and -- as demonstrated by the previous articles in this series -- leaves standard modelling machinery unchanged.

/// admonition | Insight 18. Use relevance for calibrating and selecting DB pensioner base mortality models 
    type: insight
    attrs: {id: "Insight18"}

Using the weights by $w$ and $u$ as defined in Insights&#xA0;[15](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight15) and [16](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight16) respectively to calibrate and select DB pensioner base mortality models 

- takes explicit account of liability impact, and

- defaults to sensible results regardless of the quantum of experience data available.

The following Insights need to be restated to accommodate relevance:

- [Insight&#xA0;5](/2025-08/mortality-a-over-e#Insight5) (allowing for overdispersion $\Omega$) becomes

    $$\text{Var}\big(\text{A}w-\text{E}w\big)=\Omega\,\mathbb{E}\big(\text{E}u^2\big)$$

- The equations for [Insights&#xA0;9](/2025-08/mortality-suddenly-aic#Insight9) and [Insights&#xA0;10](/2025-08/mortality-suddenly-aic#Insight10) are unchanged as

    $$\begin{aligned}
    \text{Var}\big(\hat\beta\big)\mathrel{\hat=} \Omega\,\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}
    \\[1em]
    L_\text{P}= L(\hat\beta)-\text{tr}\big(\mathbf{J}\mathbf{I}^{-1}\big)
    \end{aligned}$$

    But $\mathbf{J}$ is redefined as $\text{E}u^2XX^\text{T}$, i.e. weighted by $u^2$ rather than $w^2$.

[[All mortality insights](/collated-mortality-insights#Insight18)]

///

Being able to define a weight in this way depends on specific features of DB pensioner mortality and so it is *not* general. But there are questions to answer if actuaries do not consider for liability impact in their statistical analysis[^FullRelevance].

[^FullRelevance]: The answer may well be that the nature of the problem means that the *a priori* belief is all experience data is 100% relevant to all individuals and the time scale is such that time-relevance does not need to be allowed for.

## Alternatives

/// admonition | The infinite data fallacy
    type: danger
    attrs: {class: "inline end"}

The infinite data fallacy is that there *will* always be sufficient experience data to determine features critical to your model[^ExpertFallacy].

I've used the future tense because the assumption is usually made in the context of a hypothetical future case, as opposed to a common concrete dataset that we can all examine.

The classic example is assuming that there *will* be sufficient experience data to determine dependence of mortality on pension amount. (One of the [many](/2025-10/mortality-incoherent-rating-factors#Insight13) reasons why 'Just include pension as a covariate' is a red light.)

The point is that in the presence of infinite data *we cannot distinguish between different methods, because they will all work* (including weighting by 'anything I feel like').

///

[^ExpertFallacy]: This is closely related to the *expert fallacy*, which is:

    '*I can't define the process I will use but I am confident that I can get the right answer if I personally review every case*.'

### *1. 'Just include pension as a covariate'*

People reach for this throw away line frequently, but it's flawed.

- It doesn't specify a model to be fitted or a concrete approach. Yes, sensible approaches to allow for pension as a covariate can be constructed but they have to work very hard to avoid bias in the absence of credible experience data.

- And that leads on to the second point, which I call the 'infinite data fallacy' -- see box out. The throw away line implicitly assumes either (a)&#xA0;there is sufficient experience data to capture the impact of pension as a covariate or (b)&#xA0;if there isn't then it doesn't matter. But it most certainly *does* matter: the mortality (*μ*) rates for the CMI's standard male pensioner base tables at age 75 are over 20%(!) higher for lives-weighted vs amounts-weighted. I'd like an approach that works in all cases please.

- Allowing for 'pension as a covariate' doesn't help with other independent covariates -- you'll still end up fitting these on a lives-weighted basis.

- Finally, people tend to gloss over the [problems with using pension as a covariate](/2025-10/mortality-incoherent-rating-factors#Insight13).

### *2. Why not use a Bayesian approach instead?*

Bayesian approaches are *prima facie* attractive, and maybe they can be made to work in this context too.

Whereas relevance is a standalone concept, the definition of a prior depends on the particular mortality model (because it's a prior for the parameters of a model), which I think makes using priors less flexible and less intuitive than relevance, and probably makes the maths more messy too.

If you can get it to work then great (but note that if liability values don't figure in your approach then, whatever problem you think you've solved, it isn't pricing or valuation related).

### *3. Weight by pension amount*

In real life your systems may not be able to accommodate relevance, in which case I suggest just *weight your analysis by pension amount*[^SystemsAndWeights].

[^SystemsAndWeights]: A lot of systems can't weight by anything.

People (including me) sometimes worry about abnormally large pensions distorting the results but this hasn't been a problem in practice:

- The variance will capture concentration.
- UK DB pensions have been capped by regulation for such a long time now that extremely large pensions no longer exist.

If it's a choice between lives and amounts-weighted then, while [both are 'wrong'](https://hermiene.net/essays-trans/relativity_of_wrong.html), the amounts-weighted is safer if your objective is to value liabilities.

/// admonition | Insight 19. Prefer amounts-weighted to lives-weighted log-likelihood
    type: insight
    attrs: {id: "Insight19"}

For DB pensioner mortality analysis, prefer statistics weighted by pension amount over lives-weighted.

When there is a lot of experience data it won't matter; when there is only a little it will mitigate biased liability value estimates.

[[All mortality insights](/collated-mortality-insights#Insight19)]

///

## Conclusion

If you believe

- *what matters is valuing liabilities correctly* (as opposed to developing models that are good in some abstract sense),

- *relevance of mortality experience varies by pension amount*, and

- *the variation in pension plan mortality can be parametrised primarily in one dimension as heavy vs light mortality*,

then I think you should be using weighted log-likelihood when assessing base mortality for DB pensioners.

/// admonition | End of this series
    type: tip

This is the final article on my series on base mortality modelling in relation to DB pension plans. I hope you've found some of it interesting or even useful.

There will be more to come on mortality generally and other things too.

///
