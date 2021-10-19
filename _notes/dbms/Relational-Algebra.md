---
layout: default
name: _notes/dbms/Relational Algebra.md
categories: Databases
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
          // • rendering keys, e.g.:
          throwOnError : false
        });
    });
</script>
 - relational algebra defines a set of operations for the relational model(gives operational description of a computation, on entire tables(sets of tuples), not on indivisual tuples or cells in them)
 - the relational calculus provides a higher-level declarative language for specifying relational queries(just like sql). One example $\{S\ | \ S\in Sailors\ \exist R\in Reserves\ (R.sid= S.sid \land R.bid = 103) \}$
 - Codd's theorem established equivalence in expressivity between these two
 - 
# Relational Algebra
- algebra of opeartors on relational instances
	- closed: result of operations is also an relational instances
	- typed: input schema determines output schema(so query's legality can be checked statically)
	- no duplicate tuples in pure relational algebra
- Unary Operators
	- Projection($\pi$): retain only desired columns(vertical)
	- Selection($\sigma$): retain subset of rows(horizontal)
	- Renaming($\rho$): rename attributes and  relations
- Binary Operators
	- Union($\cup$)
	- Set difference($-$)
	- Cartesian Product($\times$)
- Compound operators: macros for the above
	- Intersection($\cap$)
	- Joins($\bowtie, \bowtie_\theta$): combine relations that satisfy predicates
<br><br>

- $\pi_{\text{sname, age}}(S)$ corresponds to `SELECT sname,age`
	- in real systems duplicates kept because of semantics and performance reason
- $\sigma_{\text{rating}>8}(S)$ corresponds to `WHERE rating>8`
	- no duplicate generated
- $R \cup S$ corresponds to `UNION`
	- `UNION ALL` for keeping duplicates
	- schema compatibility required
- $R- S$ corresponds to `EXCEPT`
	- duplicates are not gernated
	- schema compatibility required
- $R\times S$(no duplicates, compatibility not required)
- $\rho(OUT(1\rightarrow sid1, 4\rightarrow sid2), R1\times S1)$(when R1 and S1 both have sid column)
- $R \cap S = R-(R-S)$
<br>
- Theta joins($\bowtie_\theta$): cross product followed by selection on $\theta$ 
	- equi-join: theta join with theta being conjuction of equality(ANDed equals predicates)
		- natual join($\bowtie$): equi-join on all matching column names
- $R\bowtie_\theta S = \sigma_\theta (R\times S)$
- $R\bowtie S = s=\pi_\text{unique}\sigma_\text{equal matching fields}(R\times S)$
- good join algorithms are needed to avoid cross product
- example: $R1 \bowtie_{sid=sid} S1$

# Joins
- group by/aggregation($\gamma$)
	- $\gamma_\text{age,AVG(rating)}(S)$
	- $\gamma_\text{age,AVG(rating),COUNT(*)>2}(S)$ (with Having clause)