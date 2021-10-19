---
layout: default
name: _notes/dsa/C++ Things.md
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
# GCC built-ins
```
int  __builtin_popcount(unsigned int x)//returns number of 1's in x
```
```
int  __builtin_parity(unsigned int x)//parity = (number of 1's) % 2
```
```
int  __builtin_clz(unsigned int x) //count leading zeros(put l or ll for long)
```
```
int  __builtin_ctz(unsigned int x) //count trailing zeros
```
```
int  __builtin_ffs(int x) //least significant 1 bit position + 1
```

# c++ features

- constexpr for computing at compile time

```
constexpr int cube (int a){return a * a * a;}
float gk[cube(3)]; // Valid since C++11: variable 'gk' has 27 elements
```

- lambdas

```
[scope](params){body}
scope is either empty, & or =

std::function<float(float, float)> returnLambda(){
    return [](float a, float b) {return a + b;};
}
int main(){
    auto lmbda = returnLambda();
    std::cout << lmbda(8.2, 6.4) << std::endl;
}
```

- rvalue references
- structured bindings

```
auto [var1, var2] = make_tuple(1,2);//tuples,pairs,structs etc.
```