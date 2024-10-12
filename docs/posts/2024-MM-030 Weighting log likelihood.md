---
draft: true 
date: 2024-10-20
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

# Weighting log-likelihood in mortality experience analysis

Horror / dogma

Worn like a badge of honour.

Seen multiple screw up where confused.

Simple test: If you have to price a book of UK pension in payment liabilities and the only info you have is A/E do you want (a)lives-weighted or (b) amounts-weighted? If you answer (a) then I suspect that I suspect

This is not a straw man

- Aα / Eα > A1 / E1
- I've seen a major consulting firm embed something similar to this in their core modelling.

There is a fundamental point, which is that experience modelling for estimating liabilities -- fitted models are an intermediate stage.

When you have lots of experience data, the issue tends to go away if you using bands of pension amount as a rating factor -- in effect you're fitting separate models to different pension bands. (Take care though if you're selecting between continuous covariates a la Richards)

Yet 1/0 is implicit -- explicitly used by Macdonald and Richards [2024,Section 4.3] show how both left truncation and right censoring can be handled by setting an exposure
indicator function, Y (x), to zero to ‘switch off’ the contribution of exposure time and observed deaths
to estimation. Y (x) is a multiplier of µx, and to exclude the contribution of covid-affected experience,
one could also set Y (x) = 0 for the period of any mortality shocks.

Aα / Eα has its problems -- one large death or exposure can be highlighly distortionary (but this will be reflected in Eα²)

and benefits -- it is much more robust to the presence of twinning from multiple benefits for the same individual, because that doesn't affect Aα / Eα (although it does affect Eα²)

Take in steps

1. w ∈ { 0, 1} is ok. In a well-defined weighted framework this is exactly the same as including or excluding data. (In fact, that's a test that you're framework is coherent.)
1. w ∈ [0, 1] may also be ok. Known as relevance/importance
1. w ∈ [0, ∞) is the same by rescaling w ↦ w / max(w)

Financial impact

Talk about this in another post

Weights does not just mean amounts

Experience data declines in relevance

Volatility in CMI Model arising directly from data falling out of the 40 year calibration period -- crazy that events 40 years ago affect current best estimate


<!-- more -->