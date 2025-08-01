# Collated mortality insights

These are the collated mortality insights from all my blog articles.

<!-- /2025-08/mortality-measures-matter/ -->

/// admonition | Insight 1. Always allow for overdispersion
    type: insight
    attrs: {id: "Insight1"}

If you don't allow for overdispersion then you *will* underestimate uncertainty and overfit models.

[[Original article](/2025-08/mortality-measures-matter/#Insight1)]
///

/// admonition | Insight 2. Experience data is '[measurable](https://en.wikipedia.org/wiki/Measurable_space)'
    type: insight
    attrs: {id: "Insight2"}

Provided we use [measures](https://en.wikipedia.org/wiki/Measure_(mathematics)), we'll always get the same answer *regardless of how an experience dataset is partitioned*.

In particular, *there is no need for experience time periods to be contiguous*[^OmitPandemic] -- the sole requirement is that elements of the experience datasets do not intersect.

[^OmitPandemic]: An obvious example is excluding mortality experience from the height of the COVID-19 pandemic, potentially resulting in *non-contiguous* data from before and after the excluded time period.

[[Original article](/2025-08/mortality-measures-matter/#Insight2)]
///

/// admonition | Insight 3. The continuous time definitions of A and E are canonical
    type: insight
    attrs: {id: "Insight3"}

The continuous time definitions of $A$ and $E$ are measures and *the* [canonical](https://en.wikipedia.org/wiki/Canonical_form) definitions of actual and expected deaths.

Other definitions can lead to confusion -- usually over $\text{E}$ vs true expectation -- and spurious complexity.

[[Original article](/2025-08/mortality-measures-matter/#Insight3)]
///

<!-- /2025-08/mortality-a-over-e/ -->

/// admonition | Insight 4. The expected value of *A*−*E* is zero
    type: insight
    attrs: {id: "Insight4"}

If $\mu$ is the true mortality then the expected value of $\text{A}f-\text{E}f$ is zero

- *for any variable $f$* (even if $f$ was used to fit the mortality in question), and
- *for any subset* of the experience data (provided the choice of subset does not depend on E2R information).

[[Original article](/2025-08/mortality-a-over-e/#Insight4)]
///


/// admonition | Insight 5. The *same machinery* that defines *A*−*E* can be used to estimate its uncertainty
    type: insight
    attrs: {id: "Insight5"}

If $\mu$ is the true mortality then the variance of $\text{A}f-\text{E}f$ equals the expected value of $\text{E}f^2$.

(This is before allowing for overdispersion.)

[[Original article](/2025-08/mortality-a-over-e/#Insight5)]
///

/// admonition | Insight 6. A/E variance increases with concentration
    type: insight
    attrs: {id: "Insight6"}

$\sqrt{\text{E}w^2} / \text{E}w$, where $w\ge0$ is a useful and recurring measure of effective concentration in relation to mortality uncertainty. It implies that the more concentrated the experience data (in some sense) then the greater the variance of observed mortality.

Using unweighted variance without adjustment to estimate weighted statistics will likely *understate* risk.

[[Original article](/2025-08/mortality-a-over-e/#Insight6)]
///

