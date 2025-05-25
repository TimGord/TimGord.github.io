---
draft: true 
date: 2025-09-15
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

# For reasons I don't understand, the local and server slugification produce
# different results when there are dashes in the item title.
#slug: add-custom-slug-here

# Don't forget to add the <!-- more --> separator!!!
---

# Good things come to those who weight #1 / 3

There was a time when actuaries understood that amounts-weighted statistics are the most appropriate ones for assessing defined benefit pension plan liabilities. After all, 

- the objective is to value liabilities,
- liability is proportional to pension amount, and
- pension amount is a strong predictor of mortality,

 and so weighting the experience data by *what actually matters* seems sensible.

<!-- NB This does NOT show up in Markdown!! -->
<figure markdown="span">
  <figcaption">Distribution of individuals ('lives-weighted')</figcaption>
  ![Concentration](WeightLives.svg)
</figure>
<figure markdown="span">
  <figcaption">Distribution of pension amount ('amounts-weighted')</figcaption>
  ![Concentration](WeightAmounts.svg)
</figure>

But then some actuaries noticed that statistics textbooks mostly do not mention weighting by amounts, and so they decided that it must simply be wrong.

At least I think this must have happened on occasion, because I've seen some horrors in my time where actuaries discarded amounts-weighted A/E in favour of more complex lives-weighted models but, in doing so, *made their modelling worse*.

So in this note, I'm going to explain -- *mathematically* -- why using amounts-weighted statistics is a sensible starting point for mortality analysis for valuing defined benefit pension plan liabilities.

<!-- more -->

## The infinite data fallacy

I'll assume that it's *uncontentious* that pension amount is a strong predictor of mortality and therefore we have to do something to ensure that predicted mortality reflects the mortality experience for individuals with broadly similar pension amounts.

!!! question "Scenario"

    To make this more concrete, imagine a portfolio comprising two groups as follows:

    |Group|Lives|Total liability|Average liability|Proportion by lives|Proportion by liability|
    |:--:|--:|--:|--:|--:|--:|
    |A|1,500|£100m|£67k|75%|25%|
    |B|500|£300m|£600k|25%|75%|

    Would you value the liabilities  for group&#x00A0;B, i.e. 75% of the liabilities, using unweighted data, i.e. 75% based on group&#x00A0;A?[^FellForIt]

    [^FellForIt]: If your response was that of course you'd use simply use pension as a covariate then you just fell for the 'infinite data fallacy' -- see below.

Unfortunately we can't just paste in a simplistic prior assumption because the precise relationship between pension and mortality varies considerably by pension plan[^PensionAsACovariate].

[^PensionAsACovariate]:

    This is not surprising given that defined benefit pension is a function of historical benefit design and employment turnover, as well as pay history. For instance, a generous pension plan for low pay low turnover employees may result in similar pensions as an ungenerous pension plan for high pay high turnover employees.
    
    This means that it is very dangerous to use a model with a pension amount covariate fitted to one pension plan's experience data to predict the mortality in another pension plan. Indeed, in my experience, it is not even safe to assume that the dependence is monotonic, i.e. that larger pension amounts lead to lower mortality.

!!! info inline end "'Pension as a covariate'"

    What is euphemistically sometimes described as using 'pension as a covariate' is in practice challenging, even with a lot of data:

    1. **Complexity**. The dependence of mortality on pension can be complex and pension plan-specific, which means that you're selecting between multiple models, each with multiple covariates.
    1. **Historical pension increases**. Pension plans tend to provide pension amount as at the end of the experience period for survivors but as at date of death for deaths. This means pension amounts for deaths need to be revalued, which can be a nightmare (as anyone who's had to code up UK pension plan increases can attest).
    1. **Comparability over time**. For pension amounts to be comparable at different points in time they need to be adjusted for inflation (which can be material for long experience periods).
    1. **Application to non-pensions in payment**. It's hard to compute a pension amount reliably for plan members who have not yet retired. 
    Deferred pensioners by definition tend to have lower-than-average pensions because of their curtailed service and allowing for future service for active members is non-trivial This means that using pension amount as a covariate is problematic in the most common actuarial valuation scenario of all, i.e. the triennial funding valuation. 

A standard suggestion is to 'include pension as a covariate', but there is an important hidden assumption here, namely that there is sufficient experience data to do this robustly. The problem with this argument is that all sorts of methodologies can be made work in theory if we are provided with sufficient data. I call this the 'infinite data fallacy', and I'm surprised how often it crops up.

For very large (typically multiple £bns of liability) pensioner-only portfolios, yes, you can (and very often should) use covariates based on pension amount and -- provided you don't have a critical covariate fitted across pension bands -- weighting *may* not make much difference. But problems remain. First pension covariates have problems of there own, even if there is a lot of data -- see the box out. But, most importantly, using pension as a covariate does not address the core issue: using conventional lives-weighted criteria such as the [AIC](https://en.wikipedia.org/wiki/Akaike_information_criterion) to select between models means *you are not accounting for liability impact when making that choice* -- you will mostly be testing how well models perform *for the individuals have the least impact on liability value*.

So the question is this: **What can you do to ensure you have best estimate mortality *for the purpose of valuing pension plan liabilities*?**

To answer that we'll need to take a step back and consider the concept of *data relevance*.

## Relevance

Actuaries make decisions on the relevance of mortality experience data all the time.

- It is routine to exclude data from over *n* years ago over concerns that the population composition may have changed or that historical values of covariates cannot reliably be compared with current values.
- The CMI excludes data over 41 years old from its projections model[^CMIModel41Years], and, more recently, it has excluded or under-weighted data for years materially affected by COVID-19.
- The mortality experience for pensioners below age 65 in DB pension plans is usually excluded because it is distorted by the heavier mortality of ill health early retirees.
- High age (e.g. over age 95) mortality experience is routinely excluded because one unlikely survivor data error can distort a model[^HighAgeConvergence].

[^CMIModel41Years]: This results in the questionable effect that if an atypical calendar year falls off the end of the 41 year data period then this can impact the CMI Model's current predictions.

[^HighAgeConvergence]: Especially given that most models assume mortality convergence at high ages.

What's worthy of note is how very fuzzy the include / exclude decision is in contrast to the sharp-edged mathematical assumptions underlying most mortality modelling that treat data as either 100% relevant or 100% irrelevant[^AllOrNothing]. What happens if instead we try to incorporate relevance into the modelling itself? The fuzziness is not going to go away -- all we're doing is moving fuzziness already present in the real world to inside our modelling -- but maybe this approach can provide us with some insight, so let's have a go.

[^AllOrNothing]: For the avoidance of doubt, I am *not* suggesting that using a well-defined framework to obtain *initial* results and then layering in judgement on top is unreasonable, not least because it helps define and corral the fuzziness. But if it's not clear which which data to include or exclude and that has a material impact on the results, then that in itself is an implicit admission that something is missing from the modelling.

First some definitions:

1. Let $\text{Exp}$ be the *mortality experience data*[^NoTime] and $\text{Val}$ be the *valuation data*, i.e. the individuals contributing to the liabilities as at the valuation date (also sometimes known as the 'in-force data').

    [^NoTime]: I've dropped explicit references to time for simplicity. It can be accommodated by expanding experience data to include the infinitesimal time intervals to which they relate.

1. We'll assume a mortality model with a *single scalar*[^SingleFactorModel] parameter $\beta$.

    [^SingleFactorModel]: This is an important stipulation, not merely an assumption to ease tractability. I note that it works well for the valuation of large DB pension plan liabilities.

1. $\ell_i(\beta)$ is the *log-likelihood* using the mortality model for each individual in the experience data, i.e. $i\in\text{Exp}$.

1. $v_k(\beta)$ is the *liability value* using the mortality model for each individual in the valuation data, i.e. $k\in\text{Val}$.

1. $r_{ik}$ is a measure of the *relevance* of individual $i$ to individual $k$ on a scale of $0$ to $1$, where $1$ means that the individuals are statistically indistinguishable and $0$ means that information from individual $i$ cannot be used to make any inferences about individual $k$. Consistency dictates that $r_{ik}=r_{ki}$ and  $r_{ii}=1$.

We'll assume that $\ell_k(\beta)$ and $v_k(\beta)$ are sufficiently slowly varying in $\beta$, that, for 'reasonable' $\beta$, they can be approximated as quadratic and linear respectively[^SlowlyVarying].

[^SlowlyVarying]: 

    These are the simplest assumptions that capture that 
        
    - log-likelihood must have a maximum in $\beta$ in order for the model to be fitted, and
    - liability value will typically be monotonic in $\beta$.

    We also assume that $\ell_k(\beta)$ has a maximum for a finite $\beta$, or, equivalently, $\partial^2\ell_k/\partial\beta^2<0$.

For each individual $k$ in the valuation data, we can construct a log-likelihood relevant specifically to them by weighting the individual log-likelihoods

$$L_k(\beta)=\sum_{i\in\text{Exp}}r_{ik}\ell_i(\beta)$$

and hence determine their best estimate mortality as

$$\hat\beta_k=\underset{\beta}{\arg\max}\,L_k(\beta)$$

Adding up the liabilities for all individuals using their individual-specific best estimate model results in the best estimate of total liability value being

$$V=\sum_{k\in\text{Val}}v_k(\hat\beta_k)\tag{1}$$

## The one true mortality model

So far, so complex. Equation $\text{(1)}$ is *prima facie* reasonable, but also impractical in that it requires that the mortality model be fitted separately for each individual to be valued.

What we'd really like is a *single overall parameter estimate*, $\bar\beta$, that results in the same total liability value as the individual-specific best estimate mortalities, i.e.

$$\sum_{k\in\text{Val}}v_k(\hat\beta_k)=V=\sum_{k\in\text{Val}}v_k(\bar\beta)$$

If such a $\bar\beta$ exists then we can use one mortality model safe in the knowledge that it gives the correct liability value.

It turns out that, not only is this relatively straightforward, but it's equivalent to our old friend, the weighted log-likelihood, i.e.

$$\bar\beta=\argmax_\beta \left(\sum_{i\in\text{Exp}}\ell_i(\beta)w_i\right)$$

where $w_i$ is the weight.

## Derivation

(Skip this section if you're not interested in the maths.)

Using $'$ to denote differentiation with respect to $\beta$ and the above assumptions, we can expand $L_k$ around a the best estimate, $\hat\beta_k$ as

$$\begin{aligned}
L_k(\beta)
&\approx L_k(\hat\beta_k)+(\beta-\hat\beta_k)L_k'(\hat\beta_k)+\tfrac{1}{2}(\beta-\hat\beta_k)^2L''_k
\\[0.75em]
&=L_k(\hat\beta_k)-\tfrac{1}{2}(\beta-\hat\beta_k)^2I_k
\end{aligned}\tag{2}$$

where we have used the fact that, by definition, $L_k'(\hat\beta_k)=0$, and we have defined $I_k=-L_k''$, which, by assumption, is broadly independent of $\beta$ and strictly positive.

If we differentiate $\text{(2)}$ with respect to $\beta$ we find that

$$(\hat\beta_k-\beta)
\approx I_k^{-1} L_k'(\beta)$$

which allows us to re-express the total best estimate liability value as

$$\begin{aligned}
V
&=\sum_{k\in\text{Val}}v_k(\hat\beta_k)
\\[1.5em]
&\approx \sum_{k\in\text{Val}}\Big\{v_k(\bar\beta)+(\hat\beta_k-\bar\beta)v_k'\Big\}
\\[1.5em]
&\approx\sum_{k\in\text{Val}}\Big\{v_k(\bar\beta)+L_k'(\bar\beta)I_k^{-1} v_k'\Big\}
\end{aligned}$$

So the single parameter $\bar\beta$ will give the same total liability value as the individually-fitted models if

$$\sum_{k\in\text{Val}}L_k'(\bar\beta)I_k^{-1} v_k'=0\tag{3}$$

We can rearrange[^AndyHarding] the left hand side of $\text{(3)}$ as 

[^AndyHarding]: Thank you Andy Harding for pointing this out to me.

$$\begin{aligned}
\sum_{k\in\text{Val}}L_k'(\bar\beta)I_k^{-1} v_k'
&=\sum_{k\in\text{Val}}\left(\sum_{i\in\text{Exp}}r_{ik}\ell_i'(\bar\beta)\right)I_k^{-1} v_k'
\\[1.5em]
&=\sum_{i\in\text{Exp}}\ell_i'(\bar\beta)\left(\sum_{k\in\text{Val}}r_{ik}I_k^{-1} v_k'\right)
\\[1.5em]
&=\sum_{i\in\text{Exp}}\ell_i'(\bar\beta)w_i
\end{aligned}$$

where $w_i$ is defined as

$$w_i=\sum_{k\in\text{Val}}r_{ik}I_k^{-1} v_k'\tag{4}$$

So we can rewrite the condition $\text{(3)}$ that defines $\bar\beta$ as

$$\frac{\partial}{\partial\beta}\left(\sum_{i\in\text{Exp}}\ell_i(\bar\beta)w_i\right)=0$$

or, equivalently,

$$\bar\beta=\argmax_\beta \left(\sum_{i\in\text{Exp}}\ell_i(\beta)w_i\right)$$

which is nothing other than fitting our model by maximising a *weighted* log-likelihood.

## What does this look like in practice?

XXXX We don't actually have a handy relevance measure, although we can construct one

XXXX Define the assumptions, e.g.

- Single parameter family. True for pensions. (Ill health is complication but in my experience potentially dangerous for data reasons.) More than one parameter does *not* lead to a single weight.

- Pension is a strong rating factor.

XXXX Let's get a sense of what's going on in the definition of $w_i$:

XXX The following needs rewording

For a sensible model (e.g. $\mu_i \propto e^{\beta \psi_i}$, where $\psi_i$ is principally a function of age) and where the experience data and the valuation data relate to the same portfolio, we expect $I_k^{-1}$ to be broadly proportional to $\sum_{k\in\text{Val}}r_{ik}\cdot$, which means the weight is proportional to a smeared out annuity impact, which in turn means **the optimal weighting of log-likelihood in typical circumstances is proportional to smeared-out pension amount**.

Simple model:

1. Relevance behaves like a metric on (log) pension, i.e. $r_{ik}\approx 1$ if $i$ and $k$ have 'similar' pension amounts and $r_{ik}\approx 0$ if $i$ and $k$ have materially different pension amounts.

1. $v'_k=\pi_k \bar a'$
    - factor of 1.5??? error in $a$ from variation in $\mu$, i.e. much less than pension amount

1. Same average information per individual, i.e. on average $\ell''_i$ is $\bar\ell''$
    -  For flat -scaled prop hazards $\ell''$ is just expected deaths -- factor of 2 or 0.5 error in $\mu$
    -  For short time period, survival does not distort

1. Same distribution of individuals by lives and amount in experience and valuation data, i.e. 

    $$\begin{aligned}
    \sum_{k\in\text{Val}}r_{jk}&=\alpha\sum_{i\in\text{Exp}}r_{ji}
    \\[1.5em]
    \sum_{k\in\text{Val}}r_{jk}\pi_k&=\alpha\sum_{i\in\text{Exp}}r_{ji}\pi_i
    \end{aligned}$$

    - Must be true in limit of experience period being zero length and no exclusions

1. Hence

    $$\begin{aligned}
    w_i
    &=\sum_{k\in\text{Val}}\left(\frac{r_{ik}v_k'}{\sum\limits_{j\in\text{Exp}}r_{kj}(-\ell''_j)}\right)
    \\[2.5em]
    &\propto\sum_{k\in\text{Exp}}\left(\frac{r_{ik}\pi_k}{\sum\limits_{j\in\text{Exp}}r_{kj}}\right)
    \\[2.5em]
    &=\frac{\sum\limits_{k}r_{ik}\left(\pi_k\frac{N_i}{N_k}\right)}{\sum\limits_{k}r_{ik}}
    \end{aligned}$$

    where $N_i=\sum\limits_{k}r_{ik}$

    Where $r_{ik}\approx 1$, we have $N_i\approx N_k$ and so 

    $$w_i\propto \frac{\sum\limits_{k}r_{ik}\pi_k}{\sum\limits_{k}r_{ik}}$$

    i.e. the optimal weighting is proportional to 'smeared' pension amount:

Observations:

1. XXXXX r_ik=1 => lives weighted

1. This addresses the well-known concern that weighting statistics by pension amount can be distorted by unusually large pension amounts[^BenefitCap].

    [^BenefitCap]: Regulatory caps on pension amounts in the UK mean that this tends to be less of a concern these days.

1. The benefits of using a more complicated measure have to be offset against the complexity of using a non-standard weight[^AmountsDef]

    [^AmountsDef]: It's hard enough as it is to get two different actuaries to agree on A/E weighted by amounts.

## Conclusion

This is portfolio-specific. Sometimes what you want is a prior model calibrated to lots of pension schemes. In that case the test is LOO cross-validation with some generic liability measure.


Is this circular? Where do we get relevance from? Have we achieved anything. No - amounts gives better answer withno other info

Don't forget linearity, i.e. Aw / Ew is not distorted by duplicates

That said, in my experience, weighting by un-smeared amount is usually good enough.

If you only deal with large datasets, then so what.

Prior models

But not datasets are large, in fact most UK pension plans do not have enough data to set their own assumptions

In fact one of the horrors was mixing scheme-specific experience with a model

In practice

- look at A/E on amounts (distorted by errors in pension) and lives (distorted by duplicates in A or E)
- large transactions always consider PV impact
- projection is the more important assumption

Note that defined benefit pension plans are unusually tractable in there being next to no selection (in contrast to, say, individual annuities)

END ON THIS:

I think the real tell is that, if you are permitted only one A/E statistic to price a pensions portfolio, and you have to choose between amounts- and lives-weighted[^StrawMan], you will select amounts-weighted[^PriceOpportunity].

[^StrawMan]: This is not a straw man. One of the horrors to which I've referred had embedded something like this into core modelling, resulting in systematically biased results.

[^PriceOpportunity]: Or if you think you would select the lives-weighted result and you work at a life reinsurer then I can put you in touch with lots of life insurance companies that would like to do business with you.

There are issues with amounts weighting. But they're not necessarily the ones that people point about. Funniest I heard was that using lives-weighted is better because it's less uncertain. Biased as hell but hey ho.

Real actuaries do both

Good framework can do both

On the one hand, amounts-weighted A/E is a goto diagnostic, but on the other I've come up against the dogmatic rejection of weighted (log-)likelihood. The optimum is in between but, for DB pensions work, it's closer to the amounts-weighted view.

The anti-weighters seem not to understand that the purpose is financial decision making, and, in particular, that fitting a model is not the end point. I've also witness some complex procedures that could be avoided simply by understanding that weighting log-likelihood can get you there in a single step.

I've seen serious systemic errors arise from failure to understand the implications of using unweighted log-likelihood.



***TODO***


1. Note that it is amounts-*blurred* that matters. You can do this explicitly. Actual amounts has own issues.

1. Note that amounts makes more robust to duplicates. Separate, but advantageous point.

1. Note that lots of data => group by pension amount and hence issues go away.
 
1. Worry about fitting a shape to pension amount using likes-weighted.

1. You can blur pensions explicitly.

Conclude that the question is not why would you choose amounts, but why you would not in a pensions context. If you have omitted $v'$ then what you're doing is questionable.

## REST

- Aα / Eα > A1 / E1
- I've seen a major consulting firm embed something similar to this in their core modelling.

There is a fundamental point, which is that experience modelling for estimating liabilities -- fitted models are an intermediate stage.

When you have lots of experience data, the issue tends to go away if you using bands of pension amount as a rating factor -- in effect you're fitting separate models to different pension bands. (Take care though if you're selecting between continuous covariates a la Richards)

Yet 1/0 is implicit -- explicitly used by Macdonald and Richards [2024,Section 4.3] show how both left truncation and right censoring can be handled by setting an exposure
indicator function, Y (x), to zero to ‘switch off’ the contribution of exposure time and observed deaths
to estimation. Y (x) is a multiplier of µx, and to exclude the contribution of covid-affected experience,
one could also set Y (x) = 0 for the period of any mortality shocks.

Aα / Eα has its problems -- one large death or exposure can be highlighly distortionary (but this will be reflected in Eα²)

and pensions -- it is much more robust to the presence of twinning from multiple pensions for the same individual, because that doesn't affect Aα / Eα (although it does affect Eα²)

Take in steps

1. w ∈ { 0, 1} is ok. In a well-defined weighted framework this is exactly the same as including or excluding data. (In fact, that's a test that you're framework is coherent.)
1. w ∈ [0, 1] may also be ok. Known as relevance/importance
1. w ∈ [0, ∞) is the same by rescaling w ↦ w / max(w)

Financial impact

Talk about this in another post

Weights does not just mean amounts

Experience data declines in relevance

Volatility in CMI Model arising directly from data falling out of the 40 year calibration period -- crazy that events 40 years ago affect current best estimate


>**The usual rebuff: 'include your weight as a covariate'**
>The standard counter argument is that if you are concerned that there is a liability-related effect then include covariates that capture liability impact in your model. This is problematic for a number of reasons:
>1. This assumes that other covariates are *not* subject to a liability effect (and bear in mind that a lives-weighted framework cannot -- by definition -- detect this effect). So including liability-related covariates will does not by itself mean that you have a model that is fit for liability valuation.
>2. The available data is often either insufficiently credible or there are insufficiently informative rating factors. We'd like an approach that works is such cases. (Modelling is simple if you have unlimited and completely reliable data.)
>3. Determining liability-related covariates is itself not at all straightforward. There is no simple general approach. Typical approaches require clustering or selection of a function form -- these make more demands of the available information in the data.

On balance, we think weighting by pension amount is a superior default approach to lives weighting (in a pensions context). But it does rely on the following assumptions:

- Individuals with very different pension amounts likely have different mortality characteristics.
- Benefit amount is a good proxy for (a) liability impact and (b) grouping individuals.
- The distribution of pension  amounts in the experience data and the liabilities is similar.
- We don't have any extreme values (e.g. a £1m pensioner death).

We think the first two assumptions are reasonable, but the second two can present problems.

