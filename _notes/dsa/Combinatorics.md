---
layout: note
name: Combinatorics
categories: DSA
date: 2021-10-20
---
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
