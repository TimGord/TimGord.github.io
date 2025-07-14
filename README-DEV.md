# <span style="color:#00A0C0;">README-DEV</span>

<!--
#00A0C0
#FF3300
-->

## Handy ref

The Material for MKDocs is [here](https://squidfunk.github.io/mkdocs-material/getting-started/).

Lists of tags and categories to use:

|Tags|Categories|
|:---|:---|
|`Mortality`<br/>`Forecasting`<br/>`Standards`<br/>`Maths`<br/>`Coding`<br/>`Plural`|`Mortality/longevity`<br/>`Maths/stats`<br/>`Presentation`<br/>`Coding`|

## Organisation

1. Use the `dev` folder for dev associated with the blog.

2. `mkdocs.yml` controls the site generator.

3. All content lives in the `docs` folder. Posts live inside the `posts` folder (and any of its sub-folders).

## Testing the blog locally

1. Open a command prompt.

1. To check that you're up-to-date, run: `pip install mkdocs-material`

1. `cd "C:\Users\Tim Gordon\source\repos\TimGord\TimGord.github.io"`

1. To test the site:

    - Type `mkdocs serve`
    - Point your browser to [localhost:8000](http://localhost:8000/)

## NOT USED

### Syntax highlighting

**If** we want to highlight  Plural syntax using a highlighter written in JS (that can also be used with VS Code).

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
