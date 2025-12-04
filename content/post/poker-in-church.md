+++
date = "2025-12-03T11:55:40-05:00"
title = "Poker In Church"
description = "A post comparing visual and axiomatic representations of quantity."
keywords = [
  "church numerals",
  "lambda calculus",
  "functional programming",
  "poker cards",
  "triplang"
]
+++

*Look on my Works, ye Mighty, and despair!*

<small>— Ozymandias, Percy Bysshe Shelley</small>

Have you ever looked closely at a playing card? The 10 of Spades catches the eye with its inky black monolith of quantity. Though that construction is sturdy and familiar, it is also a deception. Let's examine the visual patterns in playing cards to see what they can tell us about the nature of numbers, and then contrast them with another more universal numerical representation.

![Traditional playing card pip arrangements (1-10)](/cards/cards.svg)

## A Generator Code

There are some interesting consistencies to be found in their layout. According to [Kolmogorov complexity](https://en.wikipedia.org/wiki/Kolmogorov_complexity), a thing is only as complex as the shortest program required to generate it. The fact that we can specify all ten card faces with just a handful of fractions and a few functions tells us a great deal about their structure.

{{< details title="Show Generator" >}}

```python
#! /usr/bin/env python3

from dataclasses import dataclass
from fractions import Fraction


@dataclass(frozen=True)
class Pip:
    x: float
    y: float


COL_FRACTION: dict[str, Fraction] = {
    "L": Fraction(1, 4),
    "C": Fraction(2, 4),
    "R": Fraction(3, 4),
}


RANK_PATTERNS: dict[int, list[tuple[str, int]]] = {
    1: [("C", 8)],

    2: [("C", 4), ("C", 12)],

    3: [("C", 4), ("C", 8), ("C", 12)],

    4: [("L", 4), ("R", 4),
        ("L", 12), ("R", 12)],

    5: [("L", 4), ("R", 4),
        ("C", 8),
        ("L", 12), ("R", 12)],

    6: [("L", 4), ("R", 4),
        ("L", 8), ("R", 8),
        ("L", 12), ("R", 12)],

    7: [("L", 4), ("R", 4),
        ("L", 8), ("R", 8),
        ("L", 12), ("R", 12),
        ("C", 6)],

    8: [("L", 4), ("R", 4),
        ("L", 8), ("R", 8),
        ("L", 12), ("R", 12),
        ("C", 6), ("C", 10)],

    9: [("L", 4), ("R", 4),
        ("L", 6), ("R", 6),
        ("C", 8),
        ("L", 10), ("R", 10),
        ("L", 12), ("R", 12)],

    10: [("L", 4), ("R", 4),
         ("L", 6), ("R", 6),
         ("L", 10), ("R", 10),
         ("L", 12), ("R", 12),
         ("C", 5),
         ("C", 11)],
}


def pip_centers_for_rank(
    rank: int,
    card_x: float,
    card_y: float,
    card_w: float,
    card_h: float,
) -> list[Pip]:
    pattern = RANK_PATTERNS[rank]
    step_y = card_h / 15.0
    pips: list[Pip] = []

    for col, row in pattern:
        nx = COL_FRACTION[col]
        cx = card_x + float(nx * card_w)
        cy = card_y + row * step_y
        pips.append(Pip(cx, cy))

    return pips


def generate_svg() -> str:
    width, height = 1000, 140
    card_w, card_h = 80.0, 112.0
    card_y = 14.0

    lines: list[str] = [
        '<?xml version="1.0" encoding="UTF-8"?>',
        f'<svg xmlns="http://www.w3.org/2000/svg" '
        f'viewBox="0 0 {width} {height}" width="{width}" height="{height}">',
        '  <style>',
        '    .card { fill: white; stroke: black; stroke-width: 2; }',
        '    .pip { fill: black; }',
        '  </style>',
    ]

    for rank in range(1, 11):
        card_x = 10.0 + (rank - 1) * 100.0

        lines.append(
            f'  <rect x="{card_x}" y="{card_y}" '
            f'width="{card_w}" height="{card_h}" rx="5" class="card" />'
        )

        r = 5.0
        for pip in pip_centers_for_rank(rank, card_x, card_y, card_w, card_h):
            lines.append(
                f'  <circle cx="{pip.x:.2f}" cy="{pip.y:.2f}" '
                f'r="{r}" class="pip" />'
            )

    lines.append("</svg>")
    return "\n".join(lines)


if __name__ == "__main__":
    print(generate_svg())
```

{{< /details >}}

This script generates the image above.

* **Column positions**: `1/4`, `2/4`, `3/4`—the card width is divided into four equal segments. The three pip columns are positioned at the first, second, and third quarter marks (left, center, right), leaving the fourth segment as margin on the right side.

* **Row positions**: The vertical layout uses a 14-row grid, with y-coordinates computed as `(row_index / 15) * card_height`.

* **Card aspect ratio**: `80 × 112` gives us exactly `7/5 = 1.4:1`, a ratio that appears throughout the design. The height is `112 = 16 × 7`, and the width is `80 = 16 × 5`. This ensures that when we divide the card into 8 vertical segments, each segment is exactly `14` units tall (`112 / 8 = 14`).

---

### What the Numbers Reveal

**Primality and factorization**: Composite numbers are built from simpler patterns. Rank 4 is two pairs (2×2). Rank 6 is three pairs (3×2). Rank 9 is four pairs plus a center (2×4+1). Rank 10 is two stacked 5s (2×5). The prime numbers (2, 3, 5, 7) often appear as building blocks, but notice how 7 breaks the pattern—it's 6+1, not a clean factorization.

**Symmetry and divisibility**: Even numbers (2, 4, 6, 8, 10) naturally form symmetric pairs. Odd numbers (1, 3, 5, 7, 9) require a center element to balance. The number 9 is particularly interesting: it's `3²`, and its pattern reflects this square structure, except turned on its side.

**Negative space as representation**: There's another type of representation hidden in the 8. The eight pips are arranged in two columns of three, with two center pips breaking the symmetry. But if you trace the empty space between the pips you'll find the Arabic numeral "8" in the layout itself.

---

### Pips in a Can

There's a lookup table in the code. Rank 1 is hardcoded as `[("C", 8)]`, 2 is `[("C", 4), ("C", 12)]`, and so on.

The playing card is not a pure mathematical graph, but a UX solution to the hardware limitations of our human visual cortex. We can only instantly recognize quantities up to four, and the card layout is a hack to bypass this limit. It aggregates pips into digestible sub-groups of pairs and triads so the user never has to count; a subtle compromise between visual and symbolic quantity.

And that compromise makes the system brittle. Suppose you wanted to generate Rank 11 using a function rather than a dictionary. How would the function know where to place the next pip? The rules are inconsistent: Rank 3 adds a center pip, Rank 4 switches to symmetric columns, Rank 7 breaks the symmetry entirely to fit a prime number into a rectangle. Worse, there are an infinite number of ways to arrange the pips; the layout we recognize on the cards is arbitrary, even though it does have many interesting properties.

The pictures are frozen fossil records. To further illuminate quantity we must thaw them out and consider instead the self-describing and repetitive process of becoming.

---

### Numbers as "Do This n Times"

This is the essence of **[Church encoding](https://en.wikipedia.org/wiki/Church_encoding)**: data types are defined by what they *do*, not what they *are*. A number isn't a noun, it's a verb.

Formally, a natural number `n` is a function that:

* takes a function `f`

* takes a value `x`

* and returns `f` applied to `x` **n times**.

It's a purely functional **linked list of actions**, or a mathematical **iterator pattern**.

---

## A Brief Intro to Triplang

Triplang is an experimental programming language research project I've been working on to explore combinatory logic. Unlike the playing card's static lookup table, Triplang represents numbers as executable processes—functions that compose and reduce according to the rules of [lambda calculus](https://en.wikipedia.org/wiki/Lambda_calculus). This project is part of a broader effort to design a new language that isn't pictured here yet, but the type inference system used to generate these visualizations serves as a practical tool for investigating these representational questions.

The visualizations below show how Church numerals appear as lambda terms.

**Syntax**: `λx:a.t` means "lambda x of type a, body t". The colon `:` annotates variables. The double colon `::` annotates entire terms: `t :: T` means "term t has type T". Variables get types; terms get types. Both use colons, but `::` is the assertion that binds them together.

The visualizations below show each term annotated with its inferred lambda calculus type—the type that each subexpression has during evaluation. This is distinct from the type of the final Church numeral expression itself, which represents the number's computational structure rather than the intermediate types used in its construction.

#### Zero

![Church numeral 0](/type_svg/type_0.svg)

Zero acts like a wall: it takes a function f and a value x, ignores the f entirely, and returns x. It refuses to iterate.

#### One

![Church numeral 1](/type_svg/type_1.svg)

One acts like a door: it takes f and x and lets f pass through once (f x). In combinatory logic, One corresponds to the **[I combinator](https://en.wikipedia.org/wiki/SKI_combinator_calculus)**: just as I applies its argument unchanged, One applies its function argument once.

#### Seven

{{< largeimg src="/type_svg/type_7.svg" alt="Church numeral 7" width="1200px" >}}

7 reveals linear term growth: many nodes representing the nested composition required to express "apply seven times." Each level of nesting corresponds to another application.

[Generator Code](https://github.com/maxdeliso/typed-ski/blob/0556a5eea39da4e7225934230b6daace5cff7f9b/bin/genTypeSvg.ts)

---

## The Final Fold

We've examined two radically different representations of quantity: the visual patterns on playing cards and the computational structures of Church numerals. The playing card is a lookup table optimized for instant recognition. The Church numeral is a living process that *becomes* its value through application.

Both representations reveal something fundamental about numbers: they exist simultaneously as data—static patterns to be recognized and manipulated—and as code—executable processes that generate their meaning through application. The card layout exploits our visual cortex's ability to recognize small groups, while Church numerals are built from the mathematical properties of function composition.

**Triplang as a vehicle for investigation**: The visualizations shown here are generated from Triplang's type inference system, which serves as a practical tool for exploring these representational questions. By implementing Church numerals and combinators directly in the language, we can examine how computational structures manifest visually, how type information flows through function composition, and how different encodings affect both the structure and the types of the resulting terms.

**Looking ahead**: The relationship between visual and computational representations extends beyond numbers. Boolean logic, data structures, and even [entire programs](https://en.wikipedia.org/wiki/Combinatory_logic) can be encoded entirely in functions.

**For the hackers**: If you're interested in working with these structures in TypeScript, check out [`@maxdeliso/typed-ski`](https://jsr.io/@maxdeliso/typed-ski) on JSR, which provides a type-safe implementation of combinatory logic and Church encodings.

🦀 **For the rustaceans** 🦀: For Rust developers, [`typed-ski`](https://crates.io/crates/typed-ski) is available on crates.io. That crate contains an implementation of an efficient evaluator that enables high-performance execution of combinatory structures in the browser and other WASM targets.
