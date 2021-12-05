---
layout: default
name: RahulGill
---


{% assign image_files = site.static_files | where: "image", true %}
{% for myimage in image_files %}
  {{ myimage.path }}
{% endfor %}


Hello.  

[This site's source](https://github.com/rahul-gill/Blog-and-Notes/)

My Projects:
- [College Space app](https://github.com/rahul-gill/College-Space)
- [Cricket Scoreboard app](https://github.com/rahul-gill/Scoreboard)
- p5.js Illustrations [site](https://rahul-gill.github.io/p5.js-codes/), [code](https://github.com/rahul-gill/p5.js-codes)
