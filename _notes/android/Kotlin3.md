---
layout: default
name: _notes/android/Kotlin3.md
categories: Android
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
              {left: '$', right: '$', display: false}
          ],
          // • rendering keys, e.g.:
          throwOnError : false
        });
    });
</script>
- [nulls](#nulls)
    - [checking null](#checking-null)
- [Annotations](#annotations)
- [Reflection](#reflection)

# nulls

- nullable variable `var variable: String?`

### checking null

- if blocks `if(variable == null){} else{}`
- safe calls for accessing a property on a nullable variable `a?.length` return length if not null else returns null
    
    - to perform a certain action only for non-null values
    
    ```
    item?.let{ println(it) }
    ```
    
    - safe call can also be placed in left side of assignment(if its null, the assignment is skipped & right expresion is not computed)
    
    ```
    person?.department?.head = managerPool.getManager()
    ```
    
- elvis operator `?:`"if variable is not null, use it, otherwise use some non-null value"
    
    ```
    val l = b?.length ?: -1
    ```
    
    - `throw` and `return` can also be used for the other value
- `!!` operator `b!!.length` will return a non null value of b for non null b, otherwise throws an exception

# Annotations

- mea ns of attaching meta data

# Reflection

```
(String::class).{qualifiedName, isData, isCompanion, members, constructors}
String::class.java
```

- this way functions, properties & constructors can also be referenced