+++
title = "Half Tone Shader - Part 3"
path = "posts/various/halftoneshaderp3"
date = 2022-06-02
template = "page.html"
+++

[Go to part 2](../halftoneshaderp2)

# Half Tone Shader - Color Theory

In the following, we will take a short adventure into color-theory-land ⛰️🖌️ It
will be a really short post, so sit back and relax as we look at some paint on
the screen.

## RGBA Colors

Quiet often in computer related fields, we're encountering colors as a 3D or 4D
vector (depending if you use opacity). The entries of the vector normally are
related to the RGB(A) values of the color, where

- R stands for <span style="color: rgba(255, 0, 0, 1.0)">red</span>
- G stands for <span style="color: rgba(0, 255, 0, 1.0)">green</span>
- B stands for <span style="color: rgba(0, 0, 255, 1.0)">blue</span>
- A stands for <span style="background: linear-gradient(to right, rgba(255, 255, 255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip: text;-webkit-text-fill-color: transparent;">alpha</span>

Well, ... actually ... let me
<span style="background: linear-gradient(to right, rgba(255, 0, 0, 1.0), rgba(0, 255, 0, 1.0), rgba(0, 255, 255, 1.0));-webkit-background-clip: text;-webkit-text-fill-color: transparent;"> show you the colors everywhere I can from now on.</span>

## Color Representation

There are different ways to represent colors on computers. The two main formats are:

- vector of integers (values from `0 - 255`)
- vector of floats (values from `0.0 - 1.0`)

We are going to use the float representation here and throughout the rest of
the project. 

## CYMK Colors

The original Half Tone shader I mentioned in [part
1](../halftoneshaderp1) uses another color scheme called to CYMK color scheme
opposed to the <span style="color: rgba(255, 0, 0, 1.0)">R</span><span
style="color: rgba(0, 255, 0, 1.0)">G</span><span style="color: rgba(0, 0, 255,
1.0)">B</span><span style="background: linear-gradient(to right, rgba(255, 255,
255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip:
text;-webkit-text-fill-color: transparent;">A</span> . Since we're at least as
talented as the person who wrote the
shader from the post (not actually, but let's at least pretend), we're going to
use this other color scheme aswell.

CYMK is an acronym for the colors used just as <span style="color: rgba(255, 0, 0, 1.0)">R</span><span style="color: rgba(0, 255, 0, 1.0)">G</span><span style="color: rgba(0, 0, 255, 1.0)">B</span><span style="background: linear-gradient(to right, rgba(255, 255, 255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip: text;-webkit-text-fill-color: transparent;">A</span> and here are the colors:

- C stands for <span style="color: rgba(0, 255, 255, 1.0)">cyan</span>
- Y stands for <span style="color: rgba(255, 0, 255, 1.0)">yellow</span>
- M stands for <span style="color: rgba(255, 255, 0, 1.0)">magenta</span>
- K stands for <span style="color: rgba(150, 150, 150, 1.0)">key</span> (<- "key" ... *\*psss\* ... it's just black*)

Unfortunately, we can't use these colors natively in our shaders, since the colors will be given in <span style="color: rgba(255, 0, 0, 1.0)">R</span><span style="color: rgba(0, 255, 0, 1.0)">G</span><span style="color: rgba(0, 0, 255, 1.0)">B</span><span style="background: linear-gradient(to right, rgba(255, 255, 255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip: text;-webkit-text-fill-color: transparent;">A</span> format. This leaves us with the question of how we transform 
<span style="color: rgba(255, 0, 0, 1.0)">R</span><span style="color: rgba(0, 255, 0, 1.0)">G</span><span style="color: rgba(0, 0, 255, 1.0)">B</span><span style="background: linear-gradient(to right, rgba(255, 255, 255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip: text;-webkit-text-fill-color: transparent;">A</span> 
colors to 
<span style="color: rgba(0, 255, 255, 1.0)">C</span><span style="color: rgba(255, 0, 255, 1.0)">Y</span><span style="color: rgba(255, 255, 0, 1.0)">M</span><span style="color: rgba(150, 150, 150, 1.0)">K</span> colors.

## RGBA to CYMK

What we get from our picture is a
<span style="color: rgba(255, 0, 0, 1.0)">R</span><span style="color: rgba(0, 255, 0, 1.0)">G</span><span style="color: rgba(0, 0, 255, 1.0)">B</span><span style="background: linear-gradient(to right, rgba(255, 255, 255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip: text;-webkit-text-fill-color: transparent;">A</span> 
color, but the 
<span style="background: linear-gradient(to right, rgba(255, 255, 255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip: text;-webkit-text-fill-color: transparent;">alpha</span>
part is basically always just going to be `1.0`. This is perfect since we can just discard 
<span style="background: linear-gradient(to right, rgba(255, 255, 255, 1.0), rgba(255, 255, 255, 0.35));-webkit-background-clip: text;-webkit-text-fill-color: transparent;">alpha</span>
part and use
<span style="color: rgba(255, 0, 0, 1.0)">R</span><span style="color: rgba(0, 255, 0, 1.0)">G</span><span style="color: rgba(0, 0, 255, 1.0)">B</span>
instead and the conversion from 
<span style="color: rgba(255, 0, 0, 1.0)">R</span><span style="color: rgba(0, 255, 0, 1.0)">G</span><span style="color: rgba(0, 0, 255, 1.0)">B</span>
to
<span style="color: rgba(0, 255, 255, 1.0)">C</span><span style="color: rgba(255, 0, 255, 1.0)">Y</span><span style="color: rgba(255, 255, 0, 1.0)">M</span><span style="color: rgba(150, 150, 150, 1.0)">K</span>
is significantly easier. We just can use the formulas 
```
K = 1 - max(R,G,B)
C = (1-R-K) / (1-K)
Y = (1-G-K) / (1-K)
M = (1-B-K) / (1-K)
```
[from this nice site](https://www.rapidtables.com/convert/color/rgb-to-cmyk.html) and then we are done.

And this wraps up the third part of our Half Tone Shader adventure.

[Go to part 4](../halftoneshaderp4)
