---
draft: true 
date: 2024-10-30
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

# Implementing continuous time in actuarial modelling

## The benefits of working in continuous time

<!-- more -->


Quantisation

Ironically, important to implement as integers to avoid the vagaries of floating point arithmetic. Floating point is neither *precise* -- the evaluation of *a*&#x202F;+&#x202F;*bc* depends on which machine instructions are used -- nor *associative* -- the result of (*a*&#x202F;+&#x202F;*b*&#x202F;)+*&#x202F;c* often differs from *a*&#x202F;+&#x202F;(*b*&#x202F;+*&#x202F;c*), sometimes with catastrophically so.

Nature of log-likelihood

Locality

