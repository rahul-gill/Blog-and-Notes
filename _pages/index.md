---
layout: default
name: RahulGill
title: RahulGill
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

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Frahul-gill.github.io%2FBlog-and-Notes%2F&count_bg=%2339980D&title_bg=%23000000&icon=github.svg&icon_color=%23E7E7E7&title=Hits&edge_flat=false)](https://hits.seeyoufarm.com)