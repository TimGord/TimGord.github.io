---
#draft: true 
date: 2025-09-02
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

# Mortality: Overdispersion and quasi-log-likelihood

In the first article in this series, I noted that if you don't allow for [overdispersion](/2025-08/mortality-measures-matter/#Def-overdispersion) then you *will* underestimate uncertainty and overfit models.

In this article I'll outline the most practical approach to dealing with overdispersion in a pensions longevity context.

<!-- more -->
--8<-- "snippets/mortality-series-list.md"

In the real world, mortality experience data typically exhibits *greater variance* than would be the case if, [as we've assumed](/2025-08/mortality-measures-matter/#what-is-random) up to now, that mortality itself is deterministic and deaths are independent. 

Specifically, if we define <span id="Def-overdispersion">*overdispersion*[^OverdispersionWithWeight] as</span>

$$\Omega=\frac
{\text{Var}\big(\text{A}w-\text{E}w\big)}
{\mathbb{E}\big(\text{E}w^2\big)}
\tag{16}
$$

then we typically observe $\Omega \gt 1$ instead of the $\Omega = 1$ we'd expect based on [our assumptions](/2025-08/mortality-measures-matter/#definitions).

[^OverdispersionWithWeight]: Equation $(16)$ is more general than the usual definition of overdispersion because I have included a weight $w$ so as to ensure that the additional variance of arising from variation in the weight $w$ itself is explicitly excluded.

Overdispersion tends not to affect the estimated parameters themselves (if you're using maximum likelihood), but *it does affect their estimated variance*, potentially leading to *over-fitted models* being selected if it is not accounted for. So we need to allow for overdispersion in some way.

## Causes of overdispersion

/// admonition | It's *not* heterogeneity!
    type: danger
    attrs: {class: "inline end"}

At time of writing (2025-08-20), the [Wikipedia article on overdispersion](https://en.wikipedia.org/wiki/Overdispersion) states (my italics):

> Overdispersion is a very common feature in applied data analysis *because in practice, populations are frequently heterogeneous (non-uniform) contrary to the assumptions implicit within widely used simple parametric models*.

This explanation is sometimes repeated in a mortality context, but that's *not* the case *within the framework I've outlined*: if mortality itself were deterministic ([as we've assumed](/2025-08/mortality-measures-matter/#what-is-random)) and deaths were independent then, *regardless of how much mortality varied by individual*, there would be no overdispersion. So the cause of overdispersion is not heterogeneity; it's the failure of our idealised assumptions.

And it's not just that heterogeneity in not the cause in this context; mortality varying by individual is a *core premise* in mortality modelling[^Frailty].

[^Frailty]: There are other issues caused by heterogeneity, e.g. it is a candidate for causing the flattening of observed *population* mortality at high ages and failure to allow for heterogeneity can result in the *under*-valuation of closed books of pension-in-payment liabilities. I may return to these in future articles.

///

There are multiple potential causes of overdispersion, all arising from our initial assumptions not being true in the real world, the key ones being that [mortality is itself deterministic and deaths are independent](/2025-08/mortality-measures-matter/#what-is-random). (Also see the box out regarding heterogeneity.)

We knew from the the outset that this is a convenient simplification of reality. The question is: what's the best way to allow for overdispersion[^DeduplicationFailure]?

[^DeduplicationFailure]: Duplicates in the data could be considered an extreme version of deaths not being independent, but I'll assume that every attempt has been made in practice to deduplicate the data.

## Quasi log-likelihood

One option is to use a statistical distribution with an additional hyperparameter to model observed [overdispersion explicitly](https://en.wikipedia.org/wiki/Mixed_Poisson_distribution#Properties). People do do this, but it entails a loss of tractability[^TractabilityBenefits] and there is a simpler alternative.

[^TractabilityBenefits]: Tractability doesn't just mean pretty equations; it means simpler computer code that is therefore more robust and easier to maintain, which in turn means fewer visits from the f***-up fairy.

The most widely used approach is simply to divide the log-likelihood by an estimated or assumed *global*[^GlobalOverdispersion] value for overdispersion, $\Omega$, i.e.

$$L \mapsto \Omega^{-1}L$$

[^GlobalOverdispersion]: It is not necessarily the case that overdispersion is the same for all the data in the experience dataset or for all weights in equation $(16)$, but this is a standard assumption on the basis that, once a general mitigation has been made for overdispersion, then there are probably bigger fish left to fry.

Technically, this results in a [quasi-log-likelihood](https://en.wikipedia.org/wiki/Quasi-likelihood) (and the AIC becomes the QAIC), but I'll continue to refer to it as the log-likelihood on the understanding that adjustments are always made for overdispersion.

The question naturally arises as to whether to estimate overdispersion from the experience data to hand[^OldCMIModeOverdispersion]. While measured overdispersion should be a test diagnostic (provided you have sufficient data), there is a case for using a default value:

- We have a reasonable prior view on mortality overdispersion -- it is better in general to spend your information budget on estimating the things you don't know. 
- Estimating $\Omega$ from the data can add brittleness and hard-to-explain variation to model fitting.
- Estimating $\Omega$ from the data also makes the fitting process more complicated, increasing the potential sources of error.

[^OldCMIModeOverdispersion]: I *think* the original CMI Mortality Projections Model used to do this. It doesn't any more, which means overdispersion is subsumed into its various $S$ parameters.

I'd suggest a suitable default is $2\le\Omega\le3$[^BurnhamAndersonOverdispersion], with higher values making your models a little more resistant to overfitting.

[^BurnhamAndersonOverdispersion]: Burnham & Anderson recommend $1\le\Omega\le4$ for general count data. See box out 'Overdispersed Count Data: A Review' on page 69 of Burnham, K. P.; Anderson, D. R. (2002), *Model Selection and Multimodel Inference: A practical information-theoretic approach* (2nd ed.), Springer-Verlag. doi:[10.1007/b97636](https://doi.org/10.1007/b97636), ISBN-13: 9780387953649.

If you do estimate $\Omega$ from the data then the cardinal rule is *use the same estimate for all candidate models being tested*.

/// admonition | Insight 11. Adjusting *globally* for overdispersion is reasonable and straightforward
    type: insight
    attrs: {id: "Insight11"}

If $\Omega$ is *global* overdispersion then:

1. A standard method for allowing for overdispersion is to scale log-likelihood by $\Omega^{-1}$ and variances by $\Omega$.

1. Suitable default values for mortality experience data are $2\le\Omega\le3$.

1. Use the *same $\Omega$ for all candidate models being tested*, including when $\Omega$ is being estimated from the experience data at hand.

[[All mortality insights](/collated-mortality-insights#Insight11)]
///

## Formulas including global overdispersion

For reference, I'll restate results from previous articles with overdispersion included.

1. The [variance result](/2025-08/mortality-a-over-e/#the-variance-result):

    $$\text{Var}\big(\text{A}w-\text{E}w\big)=\Omega\,\mathbb{E}\big(\text{E}w^2\big) \tag{2b}$$


1. [Log-likelihood](/2025-08/mortality-log-likelihood/#Def-log-likelihood):

    $$L=\Omega^{-1}\big(\text{A}w\log\mu-\text{E}w\big) \tag{4b}$$


1. The (estimated) [variance of the fitted covariate weights $\hat\beta$](/2025-08/mortality-suddenly-aic/#Def-Var-beta):

    $$\text{Var}\big(\hat\beta\big)\mathrel{\hat=} \Omega\,\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1} \tag{14b}$$

1. The [penalised log-likelihood](/2025-08/mortality-suddenly-aic/#Def-Var-LP) is unchanged *provided we use the adjusted (quasi-)log-likelihood from above*:

    $$L_\text{P}=L(\hat\beta)- \text{tr}\big(\mathbf{J}\mathbf{I}^{-1}\big)\tag{15b}$$

In the above, $\mathbf{I}=\text{E}wXX^\text{T}$ and $\mathbf{J}=\text{E}w^2XX^\text{T}$, and, as [previously noted](/2025-08/mortality-suddenly-aic/#just-weight-a-moment), these assume $w$ is an *ad hoc* reallocation of log-likelihood, as opposed to e.g. relevance-based.

/// admonition | Next article: *Incoherent rating factors*
    type: tip

So far I have focussed on the machinery for modelling the mortality of DB pension plan members. There is more to come on this, but in the next article I want to discuss 'incoherent rating factors'. These are rating factors that appear predictive under standard modelling methods but which, scarily, actually result in models that produce poor or even systematically biased forecasts.

///

