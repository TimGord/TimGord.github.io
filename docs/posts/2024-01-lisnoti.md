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

It's a curious thing that no-one other than coders uses [monospaced fonts](https://en.wikipedia.org/wiki/Monospaced_font) for large amounts of text. Proportional fonts are ubiquitous for the simple reason that characters such as `W` and `i` have very different natural widths. Trying to squash `W` and widen `i` so that they have same width is at best inefficient and at worst less readable. And this is before we consider non-Western languages that use characters that are wider still.

The convention that monospaced fonts are used for coding derives from programming's historical reliance on (a)&nbsp;typewriter-like terminals and displays, and (b)&nbsp;the ASCII character set (or similar), which can just about be crammed into a fixed width font.

!!! info inline end "'Typewriter habits'"

    'Typewriter habits' is a term coined by Matthew Butterick in his excellent [Practical Typography](https://practicaltypography.com/typewriter-habits.html) to describe bad typography habits left over from the typewriter era.

The case for using proportional fonts for coding -- which boils down to readability and efficiency -- has already been well made by [Martĳn Storck](https://storck.io/posts/proportional-programming-code-fonts/), [Nelson Minar](https://nelsonslog.wordpress.com/2021/09/12/proportional-fonts-and-yapf-vs-black/) and [David Jonathan Ross](https://input.djr.com/info/). But the sheer prevalence of monospaced fonts in coding contexts means that their use is often simply taken for granted and so the habit is hard to break, despite widespread IDE support for proportional fonts and despite computing having spread far beyond the English-speaking world (hello Unicode).

Very occasionally monospace is precisely what you want (e.g. to  edit hex data). But many objections to proportional fonts boil down to monospaced fonts being presumed to be better simply because either

- people are used to them, or
- some (arguably brittle) code alignment practices, e.g. [hanging comments](https://lobste.rs/s/xyeqmu/monaspace#c_zrvjdj), assume that the code will always be displayed using a monospaced font.

If you're not convinced there is a case for using proportional fonts for coding then fine; this article is not for you. But before you stop reading I make a final plea that you drop code formatting styles that assume everyone uses monospaced fonts (just like you don't format tables in documents by pressing the space bar to line up columns).

If you are still reading then there is one common objection to proportional fonts that does carry some weight, which is that many proportional (sans serif) fonts *do not reliably distinguish between characters that matter when writing code*. (In contrast, there are plenty of existing monospaced fonts that address this issue.) So let's tackle this issue first.

## The A1 AI called Al

The common problem cases for sans serif fonts are 

- upper case `I` versus lower case `l` (and while we're here, also the number `1`), and
- upper case `O` versus zero `0`. 

Gill Sans (![Characters I i 1 O and 0 in Gill Sans](Font-Il1O0-Gill-Sans-light.svg#only-light)![Characters I i 1 O and 0 in Gill Sans](Font-Il1O0-Gill-Sans-dark.svg#only-dark)) has the classic purist design where `I`, `l` and `1` are all indistinguishable.

Some sans serif fonts at least distinguish  the number `1` from letters, e.g Roboto (![Characters I i 1 O and 0 in Roboto](Font-Il1O0-Roboto-light.svg#only-light)![Characters I i 1 O and 0 in Roboto](Font-Il1O0-Roboto-dark.svg#only-dark)) -- Arial, Calibri are similar. But this still leaves `I` and `l` easily confused. 

It is worth noting that the need to distinguish similar-looking characters such as `I` v `l` is not just a coding issue -- see e.g. [this Linotype article](https://www.linotype.com/6990/din-1450.html) on redesigning Frutiger to comply with German readability standard DIN 1450.

!!! info inline end "Public signage"

    Surprisingly, many typefaces commonly used for -- sometimes even *specifically designed for* -- public signage do not reliably distinguish between the letters `I` and `l`. You can probably spot a few in [this summary](https://en.wikipedia.org/wiki/List_of_public_signage_typefaces). 
    
    Notable historical exceptions include the pioneering London Underground [Johnston typeface](https://en.wikipedia.org/wiki/Johnston_(typeface)), the UK's [Transport typeface](https://en.wikipedia.org/wiki/Transport_(typeface)) and Germany's [DIN 1451](https://en.wikipedia.org/wiki/DIN_1451).

To address the `I` v `l` issue, modern fonts often follow in the footsteps of Johnston (see the boxout) and put a tail on the lower case `l`. Unfortunately, there is a conflicting convention where the lower case `l` is tailless and serifs are added to the upper `I`. An example of this in action is the inconsistent `I` v `l` typography in Google's [Noto global fonts project](https://fonts.google.com/noto) where

- the [Western version of Noto Sans](https://fonts.google.com/noto/specimen/Noto+Sans) uses the second convention (![Characters I i 1 O and 0 in Noto Sans](Font-Il1O0-Noto-Sans-light.svg#only-light)![Characters I i 1 O and 0 in Noto Sans](Font-Il1O0-Noto-Sans-dark.svg#only-dark)), but

- the [Japanese version of Noto Sans](https://fonts.google.com/noto/specimen/Noto+Sans+JP) uses the first convention (![Characters I i 1 O and 0 in Noto Sans JP](Font-Il1O0-Noto-Sans-JP-light.svg#only-light)![Characters I i 1 O and 0 in Noto Sans](Font-Il1O0-Noto-Sans-JP-dark.svg#only-dark)).

What all this means is that, *even if `I` is distinct from `l` within a font, you cannot deduce which is which in isolation*.

Back at the start of 2017, Paul McAulay asked that [Noto Sans and Roboto be modified to correct precisely this ambiguity](https://github.com/notofonts/noto-fonts/issues/821#issuecomment-270531619) (see [here for Roboto](https://github.com/googlefonts/roboto/issues/226)), but he and others were given pretty short shrift. The justification for the status quo that I really struggle with is that *making `I` and `l` distinct cannot be done for style reasons* -- see for example [this classic](https://github.com/googlefonts/roboto/issues/226#issuecomment-270854413) and, in relation to a different font,
 [the same thing again](https://github.com/be5invis/Iosevka/issues/1352#issuecomment-1125731246).

Some proportional fonts *do* handle the `I` v `l` issue, e.g. by design [Input Sans](https://input.djr.com/info/) (although I find it too clunky for general typography and it's not open source) and [IBM Plex](https://www.ibm.com/plex/) (I suspect solely because of the IBM logo).

But fixing the `I` v `l` issue is just the start&nbsp;…

## The long wish list

As well as being suitable for writing standard computer code and as a general font, I'd also like a font that includes maths and other symbols that crop up in code documentation and, if you use [Julia](https://julialang.org/), in actual code.

The reason you want all characters in the *same* font is that otherwise the environment you're using will either show a blank character or make a -- likely garish -- substitution.

At an elementary level, using hyphen to represent a minus sign is a cardinal presentational sin given that almost all modern fonts specifically include the correct Unicode character, `U+2212` (although [Noto Sans](https://fonts.google.com/noto/specimen/Noto+Sans) has somehow managed to omit it).

Here's my personal long list of font requirements and character coverage:

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

1. [Unicode mathematical alphanumeric symbols](https://en.wikipedia.org/wiki/Mathematical_Alphanumeric_Symbols), e.g. `𝐀𝐴𝑨 𝒜𝒲𝓐 𝔄 𝔸 𝕬 𝖠𝗔𝘈𝘼 𝙰`.

!!! info "All the above *but monospaced*"

    If you want all the above but you're happy with a monospaced font, then I suggest you check out [Julia Mono](https://juliamono.netlify.app/).

## Introducing the Lisnoti font

Google's [Noto global fonts project](https://fonts.google.com/noto/) has generated sets of sans serif fonts that, in combination, have excellent Unicode coverage, which means a huge amount of the hard and technically skilled work has already been done. Further, the Noto fonts are published under an [open source licence](https://openfontlicense.org/), which means I can freely modify and republish them (provided I include the original copyright statements, any reserved font name declarations, and the original licence text).

So that's what I've done -- I've edited and combined Noto Sans fonts to create a proportional font **Lisnoti** that meets all the requirement listed in this article, including the ambiguous characters we kicked of with (![Characters I i 1 O and 0 in Lisnoti](Font-Il1O0-Lisnoti-light.svg#only-light)![Characters I i 1 O and 0 in Lisnoti](Font-Il1O0-Lisnoti-dark.svg#only-dark)).

Lisnoti can be downloaded **[here](https://github.com/Lisnoti/Lisnoti)**. 

It is available 

- in regular, *italic*, **bold** and ***bold-italic*** variants,
- in OpenType (`.ttf`) and web (`.woff` and `.woff2`) formats, and

is published under the [SIL Open Font Licence (OFL)](https://openfontlicense.org/). 

I've tested Lisnoti on Windows and Apple, but not on Linux.

If you have comments on Lisnoti, please use the [Lisnoti GitHub discussions page](https://github.com/Lisnoti/Lisnoti/discussions). (If you do then please bear in mind that I am not a typography expert.)
