---
#draft: true 
date: 2025-10-07
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

# Mortality: Incoherent rating factors

A pre-requisite for pension plan mortality modelling is that the rating factors used should be **coherent**, which I'll define shortly.

The insidious problem with incoherent rating factors is that a model *may produce poor or even systemically biased forecasts while simultaneously performing well on standard model fit and selection diagnostics*. Yikes!

For large experience datasets in particular, rating factor incoherence can be a bigger issue than model fitting and selection. But too often in practice, I've seen this issue trivialised or deemed 'obvious'.

<!-- more -->
--8<-- "snippets/mortality-series-list.md"

## Defining rating factor coherence

A rating factor is a function of the [facts available](/2025-08/mortality-measures-matter/#Def-fact) for each individual that *can validly be used as a model covariate or weight*. I suggest that all of the following[^AlreadyInFactDef] must hold in order for this to be the case:

1. No foreknowledge of death
1. Correspondence between exits and survivors
1. Comparability between individuals
1. Comparability by time

[^AlreadyInFactDef]: In theory, no foreknowledge of death and correspondence are excluded by the definition of [fact](/2025-08/mortality-measures-matter/#Def-fact). In practice, it's up to you to ensure this is actually the case.

which I'll call 'coherence'.

If you use an incoherent rating factor then you can apparently successfully crank the handle on your modelling machinery but your forecasts may be invalid. And the really scary thing is that most of the rating factors used in pension plan mortality analysis fail at least one of the above[^PensionPlanRatingFactors].

[^PensionPlanRatingFactors]: The rating factors typically available for pension plan mortality analysis are birth date, sex/gender, member category (e.g. retiree, dependant in payment, deferred or active), pension and postcode, with postcode typically being used in conjunction with third party directories of socio-economic type and other information.  You might think that at least birth date and sex/gender are reliable, but I've seen even these get mangled in practice (including on very large transactions).

/// admonition | Insight 12. Rating factors must be *coherent*
    type: insight
    attrs: {id: "Insight12"}

In order for a function of information associated with individuals to be valid as a rating factor, it must be *coherent*, which means:

1. No foreknowledge of death
1. Correspondence between exits and survivors
1. Comparability between individuals
1. Comparability by time

[[All mortality insights](/collated-mortality-insights#Insight12)]

///

The practical questions are:

- How might the available rating factors be incoherent and how can we check this?
- What can we do about it?

To illustrate the issues, I'm going to run through each of the above required properties with examples. 

### *1. No foreknowledge of death*

It is statistics 101 that you cannot use actual outcomes as model inputs. Otherwise you end up with models that perform well when tested against historical data, but this is only because they have been provided with information about the future, i.e. they are cheating.

This is not always obvious. Take, for example, the use of *health-related* socio-economic typing of postcodes. Using a health-related measure sounds like exactly the sort of thing you'd want to incorporate in a mortality model. The problem is that some of these incorporate historical mortality experience[^NotDesignedForActuaries], which is what mortality models are trying to predict. My recommendations here are:

- Check the construction of candidate socio-economic typings to ensure they do not depend on mortality data and exclude those that do.
- Consider diversifying the socio-economic typings used so as to spread the risk.

[^NotDesignedForActuaries]: This makes sense for the primary users. For health-related postcode mapping, these are organisations that want to understand the *current* state of affairs. For individual pension plan data, the primary objective is to track and administer benefits correctly.

### *2. Correspondence between exits and survivors*

At its core, all mortality analysis compares deaths to E2R, and so inconsistency between rating factors between exits and survivors may result in flawed or even biased models. Ensuring that information for exits and survivors is consistent is generally known as *the principle of correspondence*.

A classic example in relation to UK pension plans is *individual pension*, which is often provided as at the end of the experience period for survivors but as at date of exit for deaths and other exits[^NotDesignedForActuaries]. This means pensions for exits need to be adjusted ('revalued' in UK parlance). This is both finicky -- as anyone who's had to code up UK pension increases can attest -- but, at the same time, material. Standard checks are:

- Compare A/E by time period weighted by lives versus amounts.
- Compare assumed total pension in each year with the pension payments disclosed in the plan's annual accounts.

### *3. Comparability between individuals*

DB pension plan pensions in payment are a function of historical benefit design and employment turnover, as well as pay history. For instance, a generous pension plan for low pay / low turnover employees may result in similar pensions to an ungenerous pension plan for high pay / high turnover employees. This means that *it is dangerous to use pension as a rating factor across pension plans*[^InterPlanPensionComparability], e.g. as part of a general pension plan mortality model. Addressing this is hard (because pension is such a powerful predictor):

- Try using other covariates, e.g. derived from postcode, to displace pension.
- Try using a better proxy for pay or, if available, pay itself (although these come with their own problems).
- Assess model performance using [leave-one out cross validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)#Leave-one-out_cross-validation) (or some variation thereof) that reflects how the model will be used in practice[^PerPlanLOOCV].

[^InterPlanPensionComparability]: Historical company mergers or switches from DB to DC may mean that pensions are not comparable even within the *same* plan.

[^PerPlanLOOCV]: *At a minimum*, this means testing your model's performance on predicting the mortality for each plan *p* in your dataset when the model has been calibrated to data *excluding* plan *p*.

Don't forget that pension plans are *biased* subsets of all pension plan data and pricing is typically per pension plan. This means that assuming pensions in different plans are comparable leaves you exposed to the winner's curse, i.e. you win precisely the business that your model misprices against you.

### *4. Comparability by time*

The point of mortality modelling in an actuarial context is to value *future* cashflows. This means rating factors must be comparable across the historical time period against which the model is fitted *and* into the future.

For instance, in the simplistic mortality model[^Unrealistic]

[^Unrealistic]: This model is deliberately simplified. For instance, it is unrealistic to assume that mortality variation is the same at all ages.

$$\mu_{it}=\mu_{it}^\text{ref} \exp\big(\beta X_i\big)$$

there is an implicit assumption that $X_i$ has the same impact on mortality at all times in the past and in the future.

Here are some of the many ways in which this can go wrong:

- Models usually (implicitly) assume that the socio-economic factors derived from postcodes do not vary with age, and yet we all know they do: the young are typically cash-constrained, people tend to move up the 'property ladder' over their careers, and postcodes for the elderly may actually be nursing homes. In my experience, handling this issue is a combination of reviewing research to confirm that there is some stability to socio-economic type when moving home and getting comfortable that the liabilities for the young and old are not the material ones.

- It is tempting to incorporate ill health retirement indicators when analysing pensions in payment (if they are available), but, for many pension plans, the criteria for ill health retirement have been tightened up over time. This means, it may show up as predictive (in very large plans with sufficient data) but the time-based variation of the criteria may bias those predictions. I suggest testing including ill health indicators to see if the results are materially different, but regarding the model without ill health indicators as the robust one.

- The most common scenario of all for using mortality models is to value *all* of a pension plan's liabilities. But if pension is being used as a covariate then what should be used for current actives? It feels like at least some adjustment should be made to allow for potential future accrual to make them comparable to those of retirees. I don't think there is a single robust approach to this if you are using pension as a covariate.

- When trying to make pensions comparable over time, we need to consider what a pension represents. Is it (a)&#xA0;a proxy for *pay before retirement* (and therefore correlated with other forms of wealth) or (b)&#xA0;a proxy for *total income during retirement*? In the UK, this issue is usually elided because UK DB pensions are predominantly index-linked both before and after retirement. But in a pension systems where indexation of pensions in payment is less common, e.g. the USA, this may require attention. For instance, all else being equal, under interpretation&#xA0;(a), a pension of £10&#x202F;000&#xA0;pa for a 75-year old that hasn't been increased since retirement implies a relatively more wealthy individual than the same pension for a 65-year old, whereas under interpretation&#xA0;(b) no adjustment is required. There is no easy-to-determine right answer to this one.

## Pension as a rating factor

If there's a recurring theme in the above then it's this: beware of using pension as a rating factor. It will almost certainly show up as strongly predictive (regardless of whether or not you allowed for the above  issue correctly) but it can catch you out.

/// admonition | Insight 13. Take care when using pension as a rating factor
    type: insight
    attrs: {id: "Insight13"}

Be wary of phrases like 'just use pension as a covariate' because it trivialises the problems involved in making pension a *coherent* rating factor:

- Pensions for individuals in different pension plans are *not* directly comparable. For general pension plan mortality models consider using [leave-one out cross validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)#Leave-one-out_cross-validation) to understand this risk and/or using an alternative approach.
- Pensions as at date of exit need careful adjustment to be consistent with pensions of survivors (which can be non trivial for UK DB plans).
- Pensions for actives require additional consideration in relation to potential future accrual.
- Consideration needs to be given to whether or how to adjust pensions for inflation (typically since retirement). This is more of an issue in pension systems where indexation of pensions in payment is less common (e.g. the USA).
- Do not assume that longevity always increases with benefit amount.

[[All mortality insights](/collated-mortality-insights#Insight13)]

///

/// admonition | Next article: [*How mortality varies in pension plans*](/2025-10/mortality-how-mortality-varies-in-pension-plans/)
    type: tip

In the next article, I'll cover, at a high level, how mortality varies by age for DB pensioners.

With that, I'll have all the ground work in place for the final (three parter) article in this series, which I'm thinking of calling 'Good things come to those who weight'. I hope you'll find it interesting.

///
