---
#draft: true 
date: 2024-01-23
authors:
  - timgord
categories:
  - Coding
  - Presentation
#tags:
#  - Mortality
#  - Forecasting
#  - Standards
---

# Lisnoti – a proportional font that works for coding too

![Lisnoti font card](LisnotiCard.svg){ align=right }

Over the past 15 years or so, I have periodically searched in vain for a *proportional* sans serif font that is both high quality in itself but also suitable for writing computer code. I've finally decided to stop waiting and to create one myself.

The result is [Lisnoti](https://github.com/Lisnoti/Lisnoti/), which is published under the [SIL Open Font Licence (OFL)](https://openfontlicense.org/) and can be downloaded [here](https://github.com/Lisnoti/Lisnoti/). It is also the font used to typeset this blog.

<!-- more -->

## The most extreme typewriter habit of them all

It's a curious thing that no-one other than coders uses [monospaced fonts](https://en.wikipedia.org/wiki/Monospaced_font) for large amounts of text. Proportional fonts are ubiquitous for the simple reason that characters such as `W` and `i` have very different natrual widths. Trying to squash `W` and widen `i` so that they have same width is at best inefficient and at worst less readable. And this is before we consider non-Western languages that use characters that are wider still.

The convention that monospaced fonts are used for coding derives from programming's historical reliance on (a)&nbsp;typewriter-like terminals and displays, and (b)&nbsp;the ASCII character set (or similar), which can just about be crammed into a fixed width font.

!!! info "Typewriter habits"

    'Typewriter habits' is a term coined by Matthew Butterick in his excellent [Practical Typography](https://practicaltypography.com/typewriter-habits.html) to describe bad typography habits left over from the typewriter era.

The case for using proportional fonts for coding -- which boils down to readability and efficiency -- has already been well made by [Martĳn Storck](https://storck.io/posts/proportional-programming-code-fonts/), [Nelson Minar](https://nelsonslog.wordpress.com/2021/09/12/proportional-fonts-and-yapf-vs-black/) and [David Jonathan Ross](https://input.djr.com/info/). But the sheer prevalence of monospaced fonts in coding contexts means that their use is often simply taken for granted and so the habit is hard to break, despite widespread IDE support for proportional fonts and despite computing having spread far beyond the English-speaking world (hello Unicode).

Very occasionally monospace is precisely what you want (e.g. to  edit hex data). But many objections to proportional fonts boil down to monospaced fonts being presumed to be better because either

- people are already used to them, or
- some (arguably brittle and unnecessary) code alignment practices, e.g. [hanging comments](https://lobste.rs/s/xyeqmu/monaspace#c_zrvjdj), assume that the code will always be displayed using a monospaced font.

There is however one common objection to proportional fonts that does carry some weight, which is that many proportional (sans serif) fonts *do not reliably distinguish between characters that matter when writing code*. In contrast, there are plenty of existing monospaced fonts that address this issue. So let's tackle this first.

## The A1 AI called Al

The need to distinguish characters unambiguously extends beyond coding, and this is gradually being recognised -- see e.g. [this Linotype article](https://www.linotype.com/6990/din-1450.html) on redesigning Frutiger to comply with German readability standard DIN 1450.

The common problem cases for sans serif fonts are 

- upper case `I` versus lower case `l` (and while we're here, also the number `1`), and
- upper case `O` versus zero `0`. 

Gill Sans (![Characters I i 1 O and 0 in Gill Sans](Font-Il1O0-Gill-Sans-light.svg#only-light)![Characters I i 1 O and 0 in Gill Sans](Font-Il1O0-Gill-Sans-dark.svg#only-dark)) has the classic purist design where `I`, `l` and `1` are all indistinguishable.

Some sans serif fonts at least distinguish  the number `1` from letters, e.g Roboto (![Characters I i 1 O and 0 in Roboto](Font-Il1O0-Roboto-light.svg#only-light)![Characters I i 1 O and 0 in Roboto](Font-Il1O0-Roboto-dark.svg#only-dark)) -- Arial, Calibri are similar. 

But this still leaves `I` and `l` as easily confused. 

!!! info "Public signage"

    Surprisingly, many typefaces commonly used for -- sometimes even specifically designed for -- public signage do not reliably distinguish between leaves `I` and `l`. See e.g. [this summary](https://en.wikipedia.org/wiki/List_of_public_signage_typefaces). 
    
    Notable historical exceptions include the London Underground's [Johnston typeface](https://en.wikipedia.org/wiki/Johnston_(typeface)), the UK's [Transport typeface](https://en.wikipedia.org/wiki/Transport_(typeface)) and Germany's [DIN 1451](https://en.wikipedia.org/wiki/DIN_1451).

To address the `I` v `l` issue, modern fonts often follow in the footsteps of Johnston and put a tail on the lower case `l`. Unfortunately, there is a conflicting convention where the lower case `l` is tailless and serifs are added to the upper `I`. An example of this in action is the inconsistent `I` v `l` typography is Google's [Noto global fonts project](https://fonts.google.com/noto) where

- ![Characters I i 1 O and 0 in Noto Sans](Font-Il1O0-Noto-Sans-light.svg#only-light)![Characters I i 1 O and 0 in Noto Sans](Font-Il1O0-Noto-Sans-dark.svg#only-dark) are in [Noto Sans](https://fonts.google.com/noto/specimen/Noto+Sans), and

- ![Characters I i 1 O and 0 in Noto Sans JP](Font-Il1O0-Noto-Sans-JP-light.svg#only-light)![Characters I i 1 O and 0 in Noto Sans](Font-Il1O0-Noto-Sans-JP-dark.svg#only-dark) are in the Japanese version, [Noto Sans JP](https://fonts.google.com/noto/specimen/Noto+Sans+JP).

 This means that, *even if `I` is distinct from `l` within a font, you cannot deduce this without having other information about the font in use*. I'm not the first to point this out -- back in 2017, Paul McAulay asked that [Noto Sans be modified to correct precisely this ambiguity](https://github.com/notofonts/noto-fonts/issues/821#issuecomment-270531619), but he and others were given pretty short shrift.

Some proportional fonts *do* handle the `I` v `l` issue, e.g. [Input Sans](https://input.djr.com/info/) *by design* (although I find it too clunky for general typography and it's not open source) and -- I suspect solely because of the IBM logo -- [IBM Plex](https://www.ibm.com/plex/).

But disambiguating  `I` v `l` is just the start.

## The long list

As well as being suitable for writing standard computer code and as a general font, I'd also like it to incorporate maths and other symbols that crop up in code documentation (and in actual code if you use [Julia](https://julialang.org/)).

At an elementary level, using hyphen to represent a minus sign is a cardinal sin when almost all modern fonts specifically include the correct Unicode character, `U+2212` (although [Noto Sans](https://fonts.google.com/noto/specimen/Noto+Sans) has somehow managed to omit it).

Here's a final long list of requirements:

1. Reliable distinction of `I`/`l` and `O`/`0` (as already discussed).

1. Consistent arithmetic, comparison, logic, set, *n*-ary and other maths operators, e.g. 

    - arithmetic: `− × ÷ ± ∓ ∞`
    - comparison: `≤ ≠ ≥ ≈ ≡ ≢ ∝`
    - logic: `¬ ∧ ∨ ⊻ ⊤ ⊥ ⊦`
    - set: `∩ ∪ ∈ ∉ ⊂ ⊃ ⊆ ⊇ ∅`
    - n-ary: `∑ ∏ ⋀ ⋁  ⋂ ⋃`
    - other: `∫ ∂ √ Δ  ∇ ∀ ∃`

1. Greek and Cyrillic letters -- maths and logic make frequent use of Greek letters and occasionally Cyrillic ones too.

1. Consistently formatted digit and  Roman letter sub and superscripts, e.g. 

    - superscript: `⁰¹²³⁴⁵⁶⁷⁸⁹⁽⁾⁺⁻ ᵃᵇᶜᵈᵉᶠᵍʰⁱʲᵏˡᵐⁿᵒᵖʳˢᵗᵘᵛʷˣʸᶻ ᴬᴮᴰᴱᴳᴴᴵᴶᴷᴸᴹᴺᴼᴾᴿᵀᵁⱽᵂ`
    - subscript: `₀₁₂₃₄₅₆₇₈₉₍₎₊₋ ₐₑₕᵢⱼₖₗₘₙₒₚᵣₛₜᵤᵥₓ`

1. A reasonable selection of symbols, e.g.

    - squares, diamonds, rectangles, triangles, circles and stars: `■□▪▫▬▭▮▯▰▱▲△▴▵▶▷▸▹►▻▼▽▾▿◀◁◂◃◄◅◆◇◊○◌●◦◯◻◼◽◾⚪⚫⚬★☆`
    - arrows: `←↑→↓↔↕ ↖↗↘↙ ⇄ ⇅ ⇵ ⇆ ⇋⇌ ⇐ ⇒⇔ ⇦⇧⇨⇩  ￩￪￫￬`
    - ticks and crosses: `☐☑☒ ✓✔✕✖✗✘`
    - box drawing: `─│┌┐└┘├┤┬┴┼╭╮╯╰╱╲╳╴╵╶╷`
    - game characters: `♔♕♖♗♘♙♚♛♜♝♞♟ ♠♡♢♣♤♥♦♧`
    - misc but useful: `⌂☸ ♩♪♫♬♭♮♯ ♀♂⚢⚣⚤⚥⚦⚧⚨⚩⚭⚮⚯⚲ ⌘ ␣ ☉ ♿ 円圓`

1. All the operators [parsed by Julia](https://github.com/JuliaLang/julia/blob/master/src/julia-parser.scm).

1. [Unicode mathematical alphanumeric symbols](https://en.wikipedia.org/wiki/Mathematical_Alphanumeric_Symbols), e.g. `AAAA 𝐀𝐴𝑨 𝒜𝒲𝓐 𝔄 𝔸 𝕬 𝖠𝗔𝘈𝘼 𝙰`.

!!! info "All the above but monospaced"

    If you want all the above but you're happy with a monospaced font, then just use [Julia Mono](https://juliamono.netlify.app/).

## Creating the Lisnoti font

Google's [Noto global fonts project](https://fonts.google.com/noto/) has generated sets of sans serif fonts with huge Unicode coverage under an open source licence, which means most of the hard and technically skilled work has already been done.

I've edited and combined Noto Sans fonts to create a proportional font **Lisnoti** designed to meet all of the requirement listed in this article. Specifically, Lisnoti *does* handle the ambiguous characters we kicked of with: ![Characters I i 1 O and 0 in Lisnoti](Font-Il1O0-Lisnoti-light.svg#only-light)![Characters I i 1 O and 0 in Lisnoti](Font-Il1O0-Lisnoti-dark.svg#only-dark).

Lisnoti can be downloaded from [this GitHib repo](https://github.com/Lisnoti/Lisnoti) in regular, italic, bold and bold-italic variants, and in OpenType (`.ttf`) and web ()`.woff` and `.woff2`) formats. Lisnoti is issued under the [SIL Open Font Licence (OFL)](https://openfontlicense.org/). 

I've tested Lisnoti on Windows and Apple, but not on Linux.

If you have comments on Lisnoti, please use the [Lisnoti GitHub discussions page](https://github.com/Lisnoti/Lisnoti/discussions).
