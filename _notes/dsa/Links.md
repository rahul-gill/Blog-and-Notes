---
layout: default
name: _notes/dsa/Links.md
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
              {left: '\(', right: '\)', dispaly: false}
          ],
          throwOnError : true
        });
    });
    a = document.getElementById("main_content")
    console.log(a)
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(a, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false},
              {left: '\[', right: '\]', dispaly: true}
          ],
          throwOnError : true
        });
    });
</script>
https://www.cut-the-knot.org/

- Lame's Theorem
- [chicken nugget theorem](https://brilliant.org/wiki/postage-stamp-problem-chicken-mcnugget-theorem/)
- Erdős–Szekeres theorem


# Practice Routine
```
3hours a day

timing:
	div2A:	thinkMax:30min,	codingMax:30min
	div2B:	thinkMax:45min,	codingMax:30min
	div2C:	thinkMax:60-90min
	div2D:	thinkMax:600-120min(~10% 3-4hours)
WA 
	struggle for at least 15-30minutes
	if you can make brute force, check on random test cases
	otherwise check the testcases
```