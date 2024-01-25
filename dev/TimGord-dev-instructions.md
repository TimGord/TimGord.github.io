# TimGord blog instructions

## Handy ref

The Material for MKDocs is [here](https://squidfunk.github.io/mkdocs-material/getting-started/).

Lists of tags and categories to use:

|Tags|Categories|
|:---|:---|
|`Mortality`<br/>`Forecasting`<br/>`Standards`<br/>`Maths`<br/>`Coding`<br/>`Plural`|`Mortality/longevity`<br/>`Maths/stats`<br/>`Presentation`<br/>`Coding`|

## Organisation

1. Use the `dev` folder for

    - Instructions, i.e. this doc.
    - Notes and record of work associated with the blog.

2. `mkdocs.yml` controls the site generator.

3. All content lives in the `docs` folder. Posts live inside the `posts` folder (and any of its sub-folders).

## Testing the blog locally

1. Open a command prompt.

1. To check that you're up-to-date, run: `pip install mkdocs-material`

1. `cd C:\Users\Tim Gordon\source\repos\TimGord\TimGord.github.io`

1. To test the site:

    - Type `mkdocs serve`
    - Point your browser to [localhost:8000](http://localhost:8000/)

## Syntax highlighting

My plan is to rely on a Plural syntax highlighter written in JS (that can also be used with VS Code).

First, we need to ensure the Pygments highlighter is disabled. That means *not* include the following in `mkdocs.yml` in the `markdown_extensions` section:

```
  - pymdownx.highlight:
      anchor_linenums: true
      line_spans: __span
      pygments_lang_class: true
  - pymdownx.inlinehilite
  - pymdownx.superfences
```

This means that

- block code comes through on the final page like this:

    ```xml
    <pre><code class="language-plural">in Namespace

    f(x₀ ~ Real, x₁ ~ Real ) := a · b where :

        a :=  x₀ + x₁ 
        b :=  x₀ − x₁ 
    </code></pre>
    ```
- inline code comes though like this (without `pre` blocks):

    `<code>f(x) ~ Real := 1</code>`

Note that you have to handle XML entities if the code is on a web page, i.e.

|Text|Entity|
|:--:|:--:|
|`&`|`&amp;`
|`<`|`&lt;`|
|`>`|`&gt;`
|`'`|`&apos;`|
|`"`|`&quot;`|

### Attempt 1

```javascript
<script>
function syntaxHighlightPage() 
{
    var codeElements = document.getElementsByTagName("code")
    for(var i = 0; i < codeElements.length; ++i)
    {
        var codeText = codeElements[i].innerHTML;
        codeElements[i].
        codeText = '<span class="code-X">' + codeText + '</span>'
    }
}
window.addEventListener("load", syntaxHighlightPage);
</script>
```

```css
.code-X { color: #F90; }
```


## Ideas for blog posts

Note that posts can themselves be split into series.

||Category|Idea|Notes|
|:---|:---|:---|:---|
|:cinema: |Presentation|Lisnoti typeface|• Use proportional typefaces for coding<br/>• Reference 'typewriter' habits<br/>• Note space/n-dash/space is British whereas m-dash is American<br/>• Reference trend e.g. BBC etc German DIN standard<br/>• Note that Transport and, in particular, Johnston (Underground) have been in place for years<br/>• Hostility against requests to make legible (incl Google themselves)<br/>• Note that some websites now use distinguishing zero (e.g. ?)<br/>• Each of `AI` / `Al` / `A1` should be identifiable in isolation<br/>• Each of `O` / `0`<br/>• `rm` should not read as `m`<br/>• It's not all good, e.g, 'ailing', 'brilliant' -- maybe the `l` is too curly|
|:cinema: |Presentation|Use the correct characters|• Hyphen is not minus (and neither is en dash)<br/>• 'x' is not times<br/>• Use curly quotes (and single ones if you're English)<br/>• Agnostic on no space + em dash vs space + en dash + space|
|:dart: |Actuarial|Weighted mortality framework|• General args<br/>• General theory up to AIC|
|:dart: |Actuarial|A/E residuals|• $\text{A}w/\text{E}w$<br/>• Log vs abs scale and residuals<br/>• Charting -- does the dot mislead?|
|:dart: |Actuarial|Table smoothness|• Theory -- $\tau$<br/>• Examples from CMI and other base tables / projections (incl AG)<br/>• Explain why this matters for the stability of mortality projection *and* allowing for sub-annual variation<br/>• Call to action|
|:dart: |Actuarial|What's wrong with a random walk?|• Why you shouldn't use a random walk for a mortality table<br/>• You can't adjust for a specific portfolio (because the projections jump off the final points)<br/>• Show with a worked example<br/>• Secondary is that the future is featureless|
|:dart: |Actuarial|Sub-annual mortality|• Distinction between smooth underlying trend and annual/sub-annual noise<br/>• Simple model<br/>• Note flaw with Rothesay approach (i.e. tied to annual, must use smooth for projecting base tables)<br/>• Provide a TS/WASM implementation|
|:dart: |Actuarial|PV-impact relevance|• Weaknesses of simple amounts-based approach<br/>• Relevance and other approaches to fixing for this (while maintaining the benefits)|
|:dart: |Actuarial|Time-based relevance|• Annual noise vs relevance<br/>• Standard rules of thumb<br/>• How to capture the notion that experience data becomes less relevant with time|
|:dart: |Actuarial|Mortality heterogeneity|• Frailty theory<br/>• Equivalence of static and dynamic theory<br/>• Constraints on tables|
|:dart: |Actuarial|Log SMR|• Value and its uncertainty<br/>• Normal approximation for log SMR vs Poisson?<br/>• Tool to access log SMR on demand using HMD plus local TS/WASM|
|:heavy_division_sign: |Maths/stats|Equivalence of ARI and Whittaker-Henderson |• Maths<br/>• Matrix form for ARI processes<br/>• Simple for difference processes<br/>• Pro and cons -- noting that multiple ARI= ARIMA|
|:heavy_division_sign: |Maths/stats|Using the W matrix|• Motivation<br/>• Illustration|
|:heavy_division_sign: |Maths/stats|Assessing concentration|• Simple methods vs e.g. Gini coefficient<br/>• Application for actuarial work|
|:bug: |Coding|Language design|
|:bug: |Coding|Fast $q$-scaling of $\mu$ vectors|• Pade approximations<br/>• Specific considerations for scaling $\mu$ in age order (i.e. increasing magnitude)|
