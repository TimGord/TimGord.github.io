---
#draft: true 
date: 2025-10-28
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

# Mortality: Good things come to those who weight (II)

/// admonition | Maths!
    type: warning

This article is almost all maths.

If that's not your thing then I suggest that you skip ahead to [part&#xA0;III](/2025-11/mortality-good-things-come-to-those-who-weight-iii).

///

In the [previous article](/2025-10/mortality-good-things-come-to-those-who-weight-i), I noted that the the ultimate objective when assessing base pensioner mortality for DB plans is to determine *the present value of the liabilities*, as opposed to selecting and calibrating an abstract model.

In this article, I'll use a simple framework to express this mathematically.

<!-- more -->
--8<-- "snippets/mortality-series-list.md"

## Starting point

<span id="Ref-givens">These are our givens:</span>

1. The objective is to *determine the best estimate and uncertainty of the present value of liabilities*.

1. Our mortality model has a *single scalar parameter* -- see e.g. [this previous article in relation to DB pensioner mortality](/2025-10/mortality-pensioner-mortality-variation/).

1. We are *provided with a measure of relevance* -- see [the definition here](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance). This quantifies the real life fuzziness around which data to rely on when estimating mortality.

To ease the notational burden, I'll make the following simplifications:

- Instead of indexing everywhere by individual $i$ and time $t$, use a combined index, e.g. $a$. (For individuals in the valuation dataset, $t$ is always the valuation 'as at date' and so these are already effectively indexed by individual.)

- Instead of writing out integrals over time and sums over individuals for experience data, denote this using the discrete summation symbol, e.g. $\sum_a \ell_a$[^DiscreteSumVsIntegration].

- Instead of writing log-likelihood or liability value as a function of a mortality, e.g. $\ell(\mu(\beta))$ or $v(\mu(\beta))$, write them as direct functions of the mortality model parameters, e.g. $\ell(\beta)$ and $v(\beta)$.

[^DiscreteSumVsIntegration]: Sums over infinitesimals using measures are usually written with $\int$, not $\sum$. But I think using a single $\sum$ symbol makes it easier to see what's going on compared with having to write sums over individuals *and* integrals over time everywhere. If it troubles you, imagine there's a time grid with spacing $\delta t$, replace $\ell_a$ with $\delta\ell_{it}$ and take the limit $\delta t\rightarrow 0$.

Some definitions:

- We'll initially assume our mortality model takes $n$ parameters, represented as a vector, e.g. $\beta$. (We'll specialise to the $n=1$ case later.)
- $\ell_a(\beta)$ is the *log-likelihood* of the E2R for $a\in\text{Exp}$ using our mortality model, where $\text{Exp}$ is the experience data.[^DiscreteSumVsIntegration]
- $v_b(\beta)$ is the *present value of liabilities* for valuation individual $b\in\text{Val}$ using our mortality model, where $\text{Val}$ is the valuation data.
- $r_a^b\in[0,1]$ is the *[relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance)* of $a$ to $b$, with $r_a^a=1$ and $r_a^b=r_b^a$.
- $'$ and $''$ are the vector first derivative and matrix second derivative with respect to $\beta$.
- $^\text{T}$ is vector or matrix transpose.

We'll also impose the simplest non-trivial conditions to make things tractable: 

- $\ell_a(\beta)$ is approximately *quadratic*, with $\ell''_a$ negative definite, and

- $v_b(\beta)$ is approximately *linear* in $\beta$, with $v'_b$ positive[^SignVDash].

[^SignVDash]: Althought I've assumed $v'_b$ is positive, the sign of $v'_b$ doesn't matter *provided it's consistent*: if $v'_b\lt0$ then replace $v'_b$ everywhere with $-v'_b$. Indeed, because mortality models are usually arranged so that higher $\beta$ increases mortality and the liability in a pensions context is an annuity, I'd *expect* $v'_b$ to be negative.

This graphic illustrates

- indexing the <span style="color:#F28E2B;">**experience data**</span> using a (nominally discrete) single-dimensional index $a$ in place of the two dimensions of individual $i$ and (continuous) time $t$ (within each individual's E2R), and

- how elements of the <span style="color:#F28E2B;">**experience data**</span> are related by *relevance* to elements of the <span style="color:#4E79A7;">**valuation data**</span>.

![Chart illustrating relevance](Relevance-exp-val-ab-light.png#only-light)
![Chart illustrating relevance](Relevance-exp-val-ab-dark.png#only-dark)

## Roadmap

To give you an idea of where we're headed, here is a roadmap:

- First, we'll define [best estimate liability](#best-estimate-liability) taking account of relevance.

- Then we'll look for an [equivalent overall parameter](#equivalent-overall-parameter) that gives the same answer.

- To complete the circle, we'll re-express the derivation of that equivalent overall parameter in terms of [weighted log-likelihood](#weighted-log-likelihood).

- Finally, we'll address [parameter uncertainty](#parameter-uncertainty).

## Best estimate liability

The *relevant* log-likelihood for each individual to be valued $b\in\text{Val}$ is

$$L_b(\beta)=\sum_{a\in\text{Exp}}r_b^a\ell_a(\beta)\tag{17}$$

This defines an estimate of the model parameter for $b\in\text{Val}$ as

$$\hat\beta_b = \underset{\beta}{\arg\max}\, L_b(\beta)$$

or, equivalently, given that the $\ell_a$ are approximately quadratic, we can solve the differential equation

$$L'_b(\hat\beta_b)=0\tag{18}$$

The best estimate of total liability value is the sum of each individual's liabilities evaluated using the parameter fitted to the data relevant to that individual, i.e.

$$\hat V=\sum_{b\in\text{Val}}v_b(\hat\beta_b)\tag{19}$$

## Equivalent overall parameter

Equation $(19)$ is fine in theory but it'd be a nightmare to implement because each individual in the valuation data requires their own mortality model to be calibrated and selected.

What if, instead, we could derive a *single overall parameter vector estimate, $\hat\beta$, that gave the same total value*, i.e.

$$\hat V=\sum_{b\in\text{Val}}v_b(\hat\beta)$$

The assumed approximate linearity of $v_b$ means that this is equivalent to the condition

$$
0
=\sum_{b\in\text{Val}}\left\{v_b(\hat\beta)-v_b(\hat\beta_b)\right\}
\approx\sum_{b\in\text{Val}} v'_b{}^\text{T}  \left\{\hat\beta-\hat\beta_b\right\}
\tag{20}
$$

> :pushpin: $v'_b$ is written without an argument because, by assumption, it is approximately constant.

So a single overall estimate $\hat\beta$ that complies with equation $(20)$ will provide (approximately) the same overall value as the complex approach embodied in equation $(19)$.

As it stands, equation $(20)$ still requires that we calculate parameters $\hat\beta_b$ separately per individual. Is there a way to sidestep this?

One approach is to try to sum up the log-likelihoods in such a way that the maximum of that sum is automatically at $\hat\beta$. Specifically, we'd like a *scalar* weight, $\tilde w_b$ for $b\in\text{Val}$ such that equation $(19)$ holds if

$$\hat\beta=\underset{\beta}{\arg\max} \sum_{b\in\text{Val}} \tilde w_b \, L_b(\hat\beta)$$

or, equivalently,

$$\sum_{b\in\text{Val}} \tilde w_b \, L'_b(\hat\beta)=0\tag{20a}$$

The assumption that $\ell_a$ is approximately quadratic means that so is $L_b$ and hence

$$L'_b(\hat\beta) 
\approx L'_b(\hat\beta_b)+L''_b \left\{\hat\beta-\hat\beta_b\right\} 
= -I_b \left\{\hat\beta-\hat\beta_b\right\}\tag{22}$$

where I've used equation $(18)$ and, for convenience, <span id="Def-Ik">defined the *relevant information* (matrix) as<span>

$$I_b = -L''_b = -\sum_{a\in\text{Exp}}r_b^a\ell''_a \tag{23}$$

> :pushpin: $I_b$, $L''_b$ and $\ell''_a$ are written without an argument because they are approximately constant (similar to $v'_b$, as noted above).

This enables us to rewrite equation $(21a)$ approximately as

$$\sum_{b\in\text{Val}} \tilde w_b I_b \left\{\hat\beta-\hat\beta_b\right\}=0$$

and comparison with equation $(20)$ suggests[^SignVDash][^IIsIinvertible][^CreditAndyHarding]

[^IIsIinvertible]: $I_b$ is known to be invertible because it is positive definite, which in turn is because, by assumption, the $\ell''_a$ are negative definite.

[^CreditAndyHarding]: Pointed out to me by Andy Harding -- thank you.

$$\tilde w_b=I_b^{-1} v'_b\tag{24}$$

The single constraint implied by (the scalar) equation $(20)$ does not *define* a scalar weight if there are $n>1$ degrees of freedom in (the vector) equation $(21a)$. This is manifest in equation $(24)$, which defines $\tilde w_b$ as a vector, not a scalar, and in which case equation $(21a)$ would need to be rewritten as

$$\sum_{b\in\text{Val}} \tilde w_b^\text{T} \, L'_b(\hat\beta)=0\tag{21b}$$

## Weighted log-likelihood

While this approach is not fruitful in the $n>1$ case, one of our [givens above](#Ref-givens) is that our mortality model has a *single scalar parameter*, i.e. $n=1$, which means that, in our scenario, equation $(24)$ *does* define a scalar weight.

I used $\tilde w_b$ to denote the weighting of *relevant log-likelihood* indexed by *valuation* individual $b$ because I was saving $w_a$ to denote the equivalent weight indexed by *experience* datum, i.e.

$$w_a =\sum_{b\in\text{Val}} r_a^b\tilde w_b$$

Changing order of summation, we have

$$\sum_{b\in\text{Val}} \tilde w_b L_b(\beta)=\sum_{b\in\text{Val}} \tilde w_b \sum_{a\in\text{Exp}} r_b^a\ell_a(\beta)= \sum_{a\in\text{Exp}} w_a\ell_a(\beta)$$

and hence the general weight to apply to the experience data is[^CreditAndyHarding]

$$w_a=\sum_{b\in\text{Val}} r_a^b \, I_b^{-1} v'_b\tag{25}$$

/// admonition | Insight 15. Weighted log-likelihood *automatically* estimates liabilities correctly for single scalar parameter models when provided with relevance
    type: insight
    attrs: {id: "Insight15"}

If (a)&#xA0;a mortality model has a single scalar parameter and (b)&#xA0;[relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance) is provided then maximising log-likelihood weighted by

$$w_{it}=\sum_{j\in\text{Val}} r_{it}^{jt_0} \, I_j^{-1} v'_j$$

*automatically* results in the best estimate of the present value of liabilities.

In the above,

- $r_{it}^{jt_0}$ is the relevance of the log-likelihood of the E2R of individual $i$ at time $t$ to individual $j$ in the valuation data as at the valuation date $t_0$,
- $I_j$ is the relevant information matrix for valuation individual $j$, and
- $v'_j$ is derivative of liability value for valuation individual $j$ with respect to the model parameter $\beta$.

For further definitions, see [article body](/2025-10/mortality-good-things-come-to-those-who-weight-ii/#starting-point).

[[All mortality insights](/collated-mortality-insights#Insight15)]

///

I suggest that the above means is that, in a *DB pensioner* context, *if we already
have a reasonable default mortality* (so that we can calculate $I$ and $v'$) then we can define *in advance* a weight such that maximising weighted log-likelihood results in the correct overall parameter *for assessing liability value* for a single parameter model.

I'll expand on this in part&#xA0;III (to come).

## Parameter uncertainty

Best estimates are meaningless without some measure of uncertainty. I'm not going to plow through yet more maths here and so instead I'll simply state results[^Unchecked].

[^Unchecked]: These results are unchecked, so please review before you apply them.
 
We can estimate the variance in best estimate liabilities as 

$$
\text{Var}(\hat V)
\approx \sum_{b,c\in\text{Val}}v'_b{}^\text{T}\,\text{Cov}(\hat\beta_b,\hat\beta_c)\,v'_c
\approx-\!\sum_{a\in\text{Exp}}u_a^\text{T}\,\ell''_a\,u_a
$$

where $u_a$ is the vector

$$u_a=\sum_{b\in\text{Val}} \sqrt{r_a^b}\,I_b^{-1}v'_b\tag{26}$$

> :pushpin: Contrast the uncertainty weight in equation $(26)$ with the weight used to derive the best estimate in equation $(25)$ above.

Given our assumptions, we have

$$\text{Var}(\hat V)\approx V'{}^\text{T}\text{Var}(\hat \beta)V'$$

where

$$V' = \sum_{b\in\text{Val}}v'_b = -\!\sum_{a\in\text{Exp}}\ell''_a w_a $$

In the $n=1$ case, this *defines* an estimate of the variance of $\hat\beta$ as

$$\text{Var}(\hat\beta) \approx (V')^{-2}\;\text{Var}(\hat V)\tag{27}$$

/// admonition | Insight 16. A different weight is required to determine uncertainty in the presence of relevance
    type: insight
    attrs: {id: "Insight16"}

The log-likelihood weight to determine uncertainty that corresponds to the best estimate weight in [Insight&#xA0;15](/2025-10/mortality-good-things-come-to-those-who-weight-ii#Insight15) is


$$u_{it}=\sum_{j\in\text{Val}} \sqrt{r_{it}^{jt_0}} \, I_j^{-1} v'_j$$

[[All mortality insights](/collated-mortality-insights#Insight16)]

///

## Summary

Let's take stock: if our underlying mortality model has a single scalar parameter, *as is typically the case for mortality models for DB pensioners*, then 

1. maximising log-likelihood weighted by $w$ leads to the *best estimate of liability value*, and

1. the second differential of log-likelihood weighted by $u^2$ estimates *parameter (and liability) uncertainty*.

/// admonition | Next article: [*Good things come to those who weight (III)*](/2025-11/mortality-good-things-come-to-those-who-weight-iii/)
    type: tip

There remain questions of practicality and generalisability.

I'll conclude this series by addressing those questions in
[part&#xA0;III](/2025-11/mortality-good-things-come-to-those-who-weight-iii/).

///
