+++
title = "Half Tone Shader - Part 1"
path = "posts/fun/halftoneshaderp1"
date = 2022-05-24
template = "page.html"
+++

The other day I was scrolling through twitter when stumbling upon this gem of a post:

---

<br/>
<div align="center">
<blockquote class="twitter-tweet">
<p lang="und" dir="ltr">
<a href="https://t.co/cX3qavF6Qp">pic.twitter.com/cX3qavF6Qp</a>
</p>
&mdash; Franek
(@Franrekk) 
<a href="https://twitter.com/Franrekk/status/1527676092768997376?ref_src=twsrc%5Etfw">
May 20, 2022
</a>
</blockquote> 
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
</div>
<br/>

---

One of my favorite pixel artists posted a pretty cool piece of art. The
interesting part about it is, that it is rendered using a shader called the
"half-tone shader".

Since I recently read the famous

[Book of Shaders](https://thebookofshaders.com/) (which was a very plesant experience btw),

I wanted to have some fun with shaders myself and so the goal of implementing
this shader was born. To make things even more interesting I chose to implement
it in [WGSL](https://gpuweb.github.io/gpuweb/wgsl/), a shading language for the
web, primarily because I can use it in any of my
[Bevy](https://bevyengine.org/) projects but also because I might reuse it some
day for some web base projects.

This little series will cover the following topics:

1. Minimum setup of bevy to enable working on shaders 
2. A little bit of theory about the half-tone shader and colors 
3. The implementation of the shader
