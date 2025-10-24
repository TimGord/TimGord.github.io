---
#draft: true 
date: 2025-08-26
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

# Mortality: Suddenly AIC

In this article, I'm going to look at choosing between mortality models using Hirotugu Akaike's information criterion, the AIC.

I'm going to run through -- at a very high level -- the rationale behind the AIC and its construction because

- the standard result looks so trivial that people sometimes assumes it's an arbitrary convention, and
- I'm going to generalise it (a little).

<!-- more -->
--8<-- "snippets/mortality-series-list.md"

I [previously wrote](/2025-08/mortality-log-likelihood/#Ref-log) that it's a shame that the 'log' in 'log-likelihood' is not always recognised as being much more fundamental than a mere technical convenience or device for avoiding numerical under/overflow. The AIC is a case in point -- indeed Akaike himself wrote that 'the log-likelihood is essentially a more natural quantity than the simple likelihood'[^Akaike].

## Akaike's big idea

Selecting parameters by maximising the log-likelihood is an effective tool for calibrating a single model. But we can't choose between different models by comparing maximum likelihoods because models with more parameters can achieve a better fit to the data and so the comparison is not fair.

/// admonition | Further reading
    type: info
    attrs: {class: "inline end"}

Kenneth Burnham and David Anderson's [book on model selection](https://doi.org/10.1007/b97636)[^BurnhamAnderson] is in my view the best reference on *practical application* of the AIC. Also see (both online) [AIC vs BIC](http://www.sortie-nd.org/lme/Statistical%20Papers/Burnham_and_Anderson_2004_Multimodel_Inference.pdf) and [AIC myths and misunderstandings](https://sites.warnercnr.colostate.edu/kenburnham/wp-content/uploads/sites/25/2016/08/AIC-Myths-and-Misunderstandings.pdf).

Rob Hyndman provides an excellent overview of [facts and fallacies of the AIC](https://robjhyndman.com/hyndsight/aic/).

[^BurnhamAnderson]: Burnham, K. P.; Anderson, D. R. (2002), *Model Selection and Multimodel Inference: A practical information-theoretic approach* (2nd ed.), Springer-Verlag. doi:[10.1007/b97636](https://doi.org/10.1007/b97636), ISBN-13: 9780387953649.

///

Akaike's[^Akaike] insight was

- [*relative entropy*](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) (also known as the *Kullback-Leibler divergence*) can be used to compare different models,

- *maximum likelihood is a biased estimate* of the variation in relative entropy compared with reality[^TrueModel],

- provided a model is reasonably good, *we can adjust for that bias with a simple penalty*, and

- the resulting *penalised maximum log-likelihood*, $L_\text{P}$, can be used to compare models *regardless of the number of parameters they have*.

[^Akaike]: Akaike, H. (1973), "Information theory and an extension of the maximum likelihood principle", in Petrov, B. N.; Csáki, F. (eds.), *2nd International Symposium on Information Theory, Tsahkadsor, Armenia, USSR, September 2-8, 1971*, Budapest: Akadémiai Kiadó, pp. 267–281. Republished in Kotz, S.; Johnson, N. L., eds. (1992), *Breakthroughs in Statistics*, vol. I, Springer-Verlag, pp. 610–624.

[^TrueModel]: This does not imply that there is a 'true model' -- models are by definition simplifications of reality.

/// admonition | Too many twos
    type: warning
    attrs: {class: "inline end"}

The AIC is defined as −2 times $L_\text{P}$, for consistency with regression.

Unfortunately the −2 factor

- complicates the definition,
- obscures the AIC as an unbiased estimate of [relative entropy](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence), and
- leads to spurious additional factors of 2 or ½ in applications[^SpuriousTwo].

So I'll use the penalised log-likelihood, $L_\text{P}$, itself i.e. *without the −2*.
///
[^SpuriousTwo]: Examples:

    - The natural definition and standard guidance on what constitutes a significant difference in AIC is one parameter's worth. This is 1 for penalised log-likelihood, but a non-intuitive 2 for the conventional AIC definition.

    - Akaike weights, $\exp(-\tfrac{1}{2}\text{AIC})$, are pure clumsiness compared with $\exp(L_\text{P})$.

## Penalised log-likelihood

Although I'll continue to refer to 'the AIC', I'll express it in terms of penalised log-likelihood, which is equivalent but cleaner (see box out).

I'm not going to justify or derive the AIC here -- if you want further information then see Burnham & Anderson[^BurnhamAnderson] -- but we will need a few key results.

The penalised log-likelihood that falls out of the definition of [relative entropy](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) is

$$L_\text{P} \mathrel{\hat=} \mathbb{E}_1\mathbb{E}_2\,L\tag{10}$$

where

- $\mathbb{E}$ indicates expectation according to reality, which occurs once in the definition of relative entropy itself and a second time because we want our estimate to be unbiased, and

- $L$ is log-likelihood (weighted by the [variable](/2025-08/mortality-measures-matter/#Def-variable) $w\ge0$), which we [previously defined ](/2025-08/mortality-log-likelihood/#Def-log-likelihood) using the [$\text{A}$ and $\text{E}$ operators](/2025-08/mortality-measures-matter/#Def-AE-ops) as

    $$L=\text{A}w\log\mu-\text{E}w\tag{4}$$


With some assumptions, we can estimate this as

$$L_\text{P} = L(\hat\beta)-\operatorname{tr}\Big\{\mathbf{I} \, \text{Var}\big(\hat\beta\big)\Big\}\tag{11}$$

where $\beta$ parametrises the [mortality model](/2025-08/mortality-measures-matter/#Def-mortality) under consideration, $\hat\beta$ maximises the log-likelihood, and all of the following are $\dim(\beta)^2$ square matrices:

$$\begin{aligned}
\text{Var}\big(\hat\beta\big)&\approx\mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}
\\[0.5em]
\mathbf{I}&=-L''(\hat\beta)
\\[0.5em]
\mathbf{J}&\mathrel{\hat=}\mathbb{E}\,L'(\beta)L'(\beta)^\text{T}
\end{aligned}$$

with $'$ indicating $\partial /\partial \beta$ and $^\text{T}$ indicating transpose.

## The pay off

The $\mathbf{I}$ matrix and its inverse cancel in equation $(11)$, resulting in

$$L_\text{P}=L(\hat\beta)-\text{tr}(\mathbf{J}\mathbf{I}^{-1})\tag{12}$$

where $\text{tr}$ is the [trace operator](https://en.wikipedia.org/wiki/Trace_(linear_algebra)).

The usual next step is to note that, in the lives-weighted case, i.e. $w\in\{0,1\}$, $\mathbf{I}\approx\mathbf{J}$, which results in the conventional form[^MinusTwo] of the AIC:

[^MinusTwo]: Barring a factor of −2.

$$L_\text{P}=L(\hat\beta)-\dim(\beta)\tag{lives-weighted only}$$

But we can still obtain a useful result *in the ad hoc weighted case* if we assume that our mortality model is [proportional hazards](/2025-08/mortality-proportional-hazards/#the-proportional-hazards-model), i.e.

$$\mu(\beta) = \mu^\text{ref}\exp\Big(\beta^\text{T}X\Big)\tag{9}$$

From [before](/2025-08/mortality-proportional-hazards/#Def-L-dash-dash), we have

$$\mathbf{I}=-L''(\hat\beta)=\text{E}wXX^\text{T}$$

and, using the expected and variance results from the [A over E article](/2025-08/mortality-a-over-e/), we have

$$\begin{aligned}
\mathbb{E}\,L'(\beta)L'(\beta)^\text{T}
&=\mathbb{E}\big(\text{A}wX-\text{E}wX\big)\big(\text{A}wX-\text{E}wX\big)^\text{T}
\\[0.6em]
&\approx\text{E}w^2XX^\text{T}
\end{aligned}$$

resulting in our estimator for $\mathbb{E}\,L'(\beta)L'(\beta)^\text{T}$ being

$$\mathbf{J}=\text{E}w^2XX^\text{T}\tag{13}$$

<span id="Def-Var-beta">and hence</span>

$$\text{Var}\big(\hat\beta\big)\mathrel{\hat=} \big(\text{E}wXX^\text{T}\big)^{-1}\big(\text{E}w^2XX^\text{T}\big)\big(\text{E}wXX^\text{T}\big)^{-1}\tag{14}$$

<span id="Def-Var-LP">and</span>

$$L_\text{P}=L(\hat\beta)-\text{tr}\left(\frac{\text{E}w^2XX^\text{T}}{\text{E}wXX^\text{T}}\right)\tag{15}$$

/// admonition | Insight 9. An estimate of the variance of the fitted parameters for a proportional hazards mortality model is available in closed form for any *ad hoc* log-likelihood weight
    type: insight
    attrs: {id: "Insight9"}

$$\text{Var}\big(\hat\beta\big)\mathrel{\hat=} \mathbf{I}^{-1}\mathbf{J}\mathbf{I}^{-1}$$

where $\hat\beta$ is the maximum likelihood estimator of the covariate weights, $X$ is the vector of covariates, $w\ge0$ is the log-likelihood weight, $\mathbf{I}=\text{E}wXX^\text{T}$ and $\mathbf{J}=\text{E}w^2XX^\text{T}$.

(This is *before* allowing for overdispersion.)

Caveat: $w$ is an *ad hoc* reallocation of log-likelihood; it is *not* [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance).

[[All mortality insights](/collated-mortality-insights#Insight9)]
///

/// admonition | Insight 10. A penalised log-likelihood for a proportional hazards mortality model is available in closed form for any *ad hoc* log-likelihood weight
    type: insight
    attrs: {id: "Insight10"}

$$L_\text{P}= L(\hat\beta)-\text{tr}\big(\mathbf{J}\mathbf{I}^{-1}\big)$$

where $\hat\beta$ is the maximum likelihood estimator of the covariate weights, $X$ is the vector of covariates, $L$ is the log-likelihood, $w\ge0$ is the log-likelihood weight, $\mathbf{I}=\text{E}wXX^\text{T}$ and $\mathbf{J}=\text{E}w^2XX^\text{T}$.

Caveat: $w$ is an *ad hoc* reallocation of log-likelihood; it is *not* [relevance](/2025-10/mortality-good-things-come-to-those-who-weight-i/#3-defining-and-incorporating-data-relevance).

[[All mortality insights](/collated-mortality-insights#Insight10)]
///

## Just weight a moment

*Provided we use a [proportional hazards](/2025-08/mortality-proportional-hazards/#the-proportional-hazards-model) model*, we have formulas for penalised log-likelihood and the variance of $\hat\beta$ in the pragmatic *ad hoc* weighted case.

In the lives-weighted case, $w\in\{0,1\}$, then $w^2=w$ and hence equation $(15)$ collapses to the lives-weighted version. Otherwise, weighting the experience data will increase concentration and so the estimated variance of $\hat\beta$ will typically be greater than the lives-weighted version. If that reflects the impact of $\hat\beta$ on the liabilities then this is intuitively reasonable.

There are issues though:

- The validity of equation $(13)$ depends on the interpretation of the weight, $w$. If it is lives-weighted, i.e. $w\in\{0,1\}$, then everything is trivially ok. And if $w$ is an *ad hoc* reallocation of log-likelihood then this seems ok too, albeit in a pragmatic hand-wavy sense[^HandWavy]. But equation $(13)$ cannot be correct if $w$ is interpreted as, for instance, [relevance](/2025-08/mortality-log-likelihood/#Ref-relevance) -- if it were then, say, halving it should double $\text{Var}\big(\hat\beta\big)$, but it doesn't. Hence the caveats.

- Being able to rank models by $L_\text{P}$ gets us only halfway because we also need to understand *whether differences in $L_\text{P}$ are significant*. In the lives-weighted and (after we've corrected for the point above) the relevance-weighted cases, this is straightforward -- a difference in the penalised log-likelihood of 1 is significant because it is equivalent to adding one more parameter. But, for an *ad hoc* weight, using the formula as presented here does not automatically provide a measure of significance and so we need another way in[^TotalPenalityDividedByParameterCount].

[^HandWavy]: There is *non*-hand-wavy version to come in a few articles' time.

[^TotalPenalityDividedByParameterCount]: One option is to add up all the penalties of all candidate models and divide that the by the total number of parameters being fitted to obtain an equivalent to 'one more parameter'.

On balance, I think equation $(15)$ is a useful practical tool, but we can do better and put matters on a sounder footing. So I'll revisit weighting and relevance in a few articles' time.

/// admonition | Next article: [*Overdispersion and quasi-log-likelihood*](/2025-09/mortality-overdispersion-and-quasi-log-likelihood/)
    type: tip

I used variance in the above *with allowing for overdispersion*. This is [*always wrong in mortality analysis*](/2025-08/mortality-measures-matter/#Insight1), so let's tackle it in the [next article](/2025-09/mortality-overdispersion-and-quasi-log-likelihood/).

///

