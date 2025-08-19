---
#draft: true 
date: 2025-08-19
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

# The local and server slugifications produce *different* results when there are dashes in the item title.
#slug: add-custom-slug-here

# Don't forget to add the <!-- more --> separator!!!
---

# Mortality: Proportional hazards

[A/E diagnostics](/2025-08/mortality-a-over-e/) are important but, if we have *any* mortality experience data, we should be using it to develop a model that takes account of that data, even if it's nothing more than a simple *how-much-heavier-or-lighter-is-the-mortality-of-this-population-than-average* model. Otherwise, we're not making full use of available information.

There are lots of possible approaches, including complex parametric formulas designed to capture all typically observed effects. But I promised [concision](/2025-08/mortality-measures-matter/#Ref-concision) and so in this article I'll expound what I think is simultaneously one of the most powerful general approaches and one of the simplest. And the beauty of it is: we've already done most of the work.

<!-- more -->
--8<-- "snippets/mortality-series-list.md"

To make this concrete, let's assume that we have a mortality model $\mu(\beta)$, where $\beta$ is a vector of real parameters[^MortalityHasFactAndTimeArgs] and that $\mu(\beta)$ is differentiable by $\beta$.

[^MortalityHasFactAndTimeArgs]: Recall that, as a [mortality](/2025-08/mortality-measures-matter/#Def-mortality), $\mu(\beta)$ also has implicit [fact](/2025-08/mortality-measures-matter/#Def-fact) ($i$) and time ($t$) arguments, but these are not shown to ease the notational burden.

A standard way to estimate $\beta$ is to choose the value that maximises the log-likelihood, i.e.

$$\hat\beta = \underset{\beta}{\text{arg max}}\, L(\beta)\tag{5}$$

where $L$ is the log-likelihood and the parameter $\beta$ is passed through to the mortality $\mu(\beta)$.

We saw in the previous article that the [log-likelihood](/2025-08/mortality-log-likelihood/#Def-log-likelihood) is

$$L=\text{A}w\log\mu-\text{E}w\tag{4}$$

where $w\ge0$ is an optional weighting [variable](/2025-08/mortality-measures-matter/#Def-variable).

We can try solving equation $(5)$ by setting the derivative of $L$ to zero, resulting in the vector differential equation

$$\frac{\partial L}{\partial\beta}(\hat\beta) = 0\tag{6}$$

Recalling that there is an implicit $\mu(\beta)$ in the [$\text{E}$ operator](/2025-08/mortality-measures-matter/#Def-E-op), we can re-express the derivative as

$$\frac{\partial L}{\partial\beta}=\text{A}wX-\text{E}wX\tag{7}$$

where

$$X=\frac{1}{\mu}\frac{\partial \mu}{\partial\beta}\tag{8}$$

## The proportional hazards model

The form of equation $(7)$ begs the question: *what if the vector $X$ were an object in its own right, independent of $\beta$, i.e. $\partial X/ \partial\beta=0$?* 

Equation $(8)$ would then be a simple first order differential equation in $\mu$, for which we can write down the solution as

$$\mu(\beta) = \mu^\text{ref}\exp\Big(\beta^\text{T}X\Big)\tag{9}$$

where

- $\mu^\text{ref}$ is a mortality that does not depend on $\beta$, and
- $\beta^\text{T}X$ means the ['dot' product](https://en.wikipedia.org/wiki/Dot_product) of vectors $\beta$ and $X$, i.e. $\sum_j \beta_j X_j$,

and don't forget that $\mu$, $\mu^\text{ref}$ and the components of the covariate vector $X$ are all [variables](/2025-08/mortality-measures-matter/#Def-variable) and therefore also have implicit [fact](/2025-08/mortality-measures-matter/#Def-fact) ($i$) and time ($t$) arguments.

<span id="Def-proportional-hazards">Equation $(9)$ is the well-known *proportional hazards model*[^PartialLikelihood]</span>, with the elements of $X$ being the covariates and the elements of $\beta$ the fitted covariate weights.

[^PartialLikelihood]: It is sometimes called the '*Cox* proportional hazards model', but this is usually in a context (e.g. medical research) where the objective is to estimate an impact independently from the absolute mortality (or 'hazard') rate, which is the *opposite* of what we're doing here -- we are trying to calculate the *absolute* hazard rate.

Some observations:

- Let's first note that we were led to equation $(9)$ simply by writing the log-likelihood in terms of the [$\text{A}$ and $\text{E}$ operators](/2025-08/mortality-measures-matter/#Def-AE-ops) and the symmetry of equation $(7)$.

- The name 'proportional hazards' is not ideal because hazard[^Decrements] rates combine using *addition*, i.e. $\mu=\sum_i\mu^{(i)}$, *not* multiplication, i.e. $\mu=\prod_i e^{\beta_iX_i}$. So the covariates do *not* relate to individual hazard rates; instead they are component effects used to build a model. The ultimate justification is the effectiveness -- power and tractability -- of the proportional hazards approach.

- Finally, this is part of a bigger picture in which *linear $\log\mu$[^LogLog] models are ubiquitous*, from [Gompertz](https://doi.org/10.1098/rstl.1825.0026), arguably the world's first realistic mortality model, via [Lee-Carter](https://doi.org/10.2307/2290201)[^LeeCarter] to the [CMI Mortality Projections Model](https://www.actuaries.org.uk/learn-and-develop/continuous-mortality-investigation/cmi-investigations/mortality-projections).

[^Decrements]: 'Hazard' is the general term for what actuaries often call a 'decrement'.

[^LogLog]: When you think about it, it is a little odd that $\log\mu$ should be the natural metric when $\mu$ itself is already the log of something. There is a very good explanation for this, but that's to come.

[^LeeCarter]: OK, Lee-Carter is bi-linear.

We still need to solve equation $(6)$, which, in general, we have to do numerically. The good news is that, *provided we use a proportional hazards model*, we can write down the first and second derivative of the log-likelihood in closed form. That in turn means we can use [Newton–Raphson](https://en.wikipedia.org/wiki/Newton%27s_method_in_optimization), which, in my experience is robust[^NewtonRaphsonRobustness] and beats most other numerical methods hands down[^NewtonRaphsonOptimal]. The vector first derivative (from equation $(7)$ above) and the matrix <span id="Def-L-dash-dash">second derivative</span> are

$$\begin{aligned}
L'&=\text{A}wX-\text{E}wX
\\[1em]
L''&=-\text{E}wXX^\text{T}
\end{aligned}$$

where I have used $'$ to indicate $\partial/\partial\beta$.

At risk of repetition, note (a)&#xA0;the concision and (b)&#xA0;that everything can be expressed in terms of $\text{A}$ and $\text{E}$, which means we're re-using  existing machinery for these calculations.

[^NewtonRaphsonRobustness]: Some initial damping may be required.

[^NewtonRaphsonOptimal]: First, it converges *quadratically*, and, second, the cost of inverting $L''$ (sometimes cited as a reason not to use Newton-Raphson) is typically very small compared with the cost of calculating $L'$ and $L''$. Failure to converge is often because the model being calibrated has an identifiability issue, resulting in $L''$ being non-invertible, which in itself is a handy diagnostic.

## Information budget

Many expositions of the proportional hazards model do not include $\mu^\text{ref}$, i.e. a given 'background' hazard rate, but for mortality analysis this is often optimal. For instance:

- If you're analysing DB pension plan mortality experience over the last ten years, you probably don't want to be trying to calibrate a mortality *trends* model[^MortalityTrendsModel] at the same time, which you can avoid by putting your pre-existing mortality trends assumption into $\mu^\text{ref}$.

- I'd suggest going further and modelling *variation* from a reasonable default mortality (including trends) so that you inherit *a priori* sensible behaviour[^HighAgeMortality] from that default.

- At the most extreme, if you have a postcode mortality model to hand, then use that as $\mu^\text{ref}$[^Credibility].

[^MortalityTrendsModel]: Mortality trends are hard to discern over a period as short as ten years, and breaking them down into age, period and cohort components is even harder. The CMI uses 41 years for its [Mortality Projections Model](https://www.actuaries.org.uk/learn-and-develop/continuous-mortality-investigation/cmi-investigations/mortality-projections) and it still struggles. Trends also typically include some element of judgement or consensus view, to which you likely want to adhere in your general mortality modelling.

[^HighAgeMortality]:
    An important example is *high age mortality*, which is nigh impossible  to determine from an individual pension plan's experience data. By the very nature of high ages, there is little data and that data is often unreliable -- even if the plan is huge, systemic data risk remains. So, in practice, you *will* need some sort of prior model for high age mortality.
  
    This is one of the reasons I set up the CMI's High Age Mortality Working Party. The high quality -- and [prize-winning](https://actuaries.org.uk/about-us/prizes-and-awards/best-paper-prizes/peter-clark-prize/) -- reports ([WP100](https://actuaries.org.uk/media/cfxilsvc/cmi-wp100-v01-2017-06-30-hamwp-second-report.pdf) and [WP122](https://www.actuaries.org.uk/system/files/field/document/CMI%20WP122%20v01%202019-06-28%20%20HAMWP%20final%20report_0.pdf)) by [Steve Bale](https://www.linkedin.com/in/steve-bale-5785b521/) and his working party colleagues are recommended reading.
  
    Incidentally, 'high age' in a pension plan longevity context typically means over age 90 or so.

[^Credibility]: In this case, you'll also need a means of determining the weight to place on the experience compared with your postcode model, but that's a separate subject.

In general, you want to spend the information budget provided by the experience data on fitting the unknowns you don't know as opposed to spending it on refitting things you likely already do. The proportional hazards model makes this straightforward.

## One model to rule them all

In my experience, the proportional hazards model is all you need in practice. The richness available from the infinite range of possible covariates, the sheer tractability of the approach, the straightforwardness of using a prior model and the interpretability of the results provide enough firepower to tackle any real world mortality modelling problem.

/// admonition | Insight 8. Proportional hazards models are probably all you need for mortality modelling
    type: insight
    attrs: {id: "Insight8"}

The proportional hazards model

$$\mu(\beta) = \mu^\text{ref}\exp\Big(\beta^\text{T}X\Big)$$

is

- highly tractable, and
- sufficiently powerful to cope with almost all practical mortality modelling problems.

[[All mortality insights](/collated-mortality-insights#Insight8)]
///

There is a lot more to this, e.g. how does mortality vary between populations, do we require additional procedures to select covariates initially, and so on, which are questions I hope to answer in due course.

But, for now, let's take stock:

- With the proportional hazards model, we have an excellent framework for creating mortality models.
- And, by maximising log-likelihood, we can calibrate those models with relative ease.

The obvious next question is: *how should we choose between different models?* This will be the subject of the next article.
