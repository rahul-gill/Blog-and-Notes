---
layout: default
name: _notes/dsa/Combinatorics.md
categories: DSA
date: 2021-10-19
---
<script 
    type="text/javascript"
    src="https://unpkg.com/mermaid@8.13.2/dist/mermaid.min.js">
</script>

<link 
  rel="stylesheet" 
  href="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.css" integrity="sha384-zTROYFVGOfTw7JV7KUu8udsvW2fx4lWOsCEDqhBreBwlHI4ioVRtmIvEThzJHGET" crossorigin="anonymous">

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.js" integrity="sha384-GxNFqL3r9uRJQhR+47eDxuPoNE7yLftQM8LcxzgS4HT73tp970WS/wV5p8UzCOmb" crossorigin="anonymous">
</script>

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/contrib/auto-render.min.js" integrity="sha384-vZTG03m+2yp6N6BNi5iM4rW4oIwk5DfcNdFfxkk9ZWpDriOkXX8voJBFrAO7MpVl" crossorigin="anonymous">
</script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false},
              {left: '\[', right: '\]', dispaly: true}
          ],
          throwOnError : false
        });
    });
</script>
# Counting
- **basic counting principle**: $r$ stages with $n_i$ choices at state $i$; the total number of choices are $n_1\cdot n_2\cdot n_3\cdot ...\cdot n_r$
- **permutations**: number of ways of ordering the n items: $n!$
- number of  ordered sequences of k elements from an n-element set is $\frac{n!}{(n-k)!}$
- **combinations** $\binom{n}{k}$: number of k element subsets of n-element set
$$\binom{n}{k} = \frac{n!}{k!(n-k)!}
$$
- these also called **binomial coefficients** because
$$(a+b)^n =  \sum\limits_{k=0}^n{\binom{n}{k}a^kb^{n-k}}
$$
-  $$\text{total number of subsets} = \sum\limits_{k=0}^n{\binom{n}{k}} = 2^n
$$
<br>

- $n$ distinct items; $r$ persons; $i^{th}$ person given $n_i$ items, then number of partitions(multinomial coefficient)
$$ \text{number of partitions}= \frac{n!}{n_1!\cdot n_2!\cdot ...\cdot n_r!}
$$
