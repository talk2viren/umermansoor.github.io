---
layout: post
title: Tutorial - Configuring Photoshop for 2D Pixel Art
comments: True
excerpt_separator: <!--more-->
---

I'm a huge fan of retro video games and pixel art. Over the christmas break, I tried (after a long hiatus) to create some pixel art for a retro-style 2D game I was building in Unity for fun. I had to struggle a little in setting up Photoshop to create 2D sprites and the background, so here's a quick step-by-step tutorial on how to configure Photoshop to create pixel art.

<!--more-->

## Step 1: Create a Tiny Image

Pixel art is done in very low resolutions. What this means is that you'll start by creating a **very small image, one that you can barely see without zooming in**. I can't give you a rule of thumb, but I generally use 20x20 pixels for sprites (sometimes 40x40 pixels if I want to put in more details) and about 150x80 pixels for backgrounds.

So go ahead and create a new image in Photoshop. The screenshot below is for creating a 20x20 pixels sprite.

![photoshop-pixel-art]({{ site.url }}/img/px-pixel/1.png)

After you have created the image, you'll be able to see it barely. So zoom in so you are able to see it.

![photoshop-pixel-art]({{ site.url }}/img/px-pixel/1-zoom.png)

## Step 2: Setup Image Interpolation to Nearest Neighbors

When your pixel art is resized or scaled, you'll want the edges or corners to look hard and jagged instead of smooth and blurred. Be default, Photoshop uses Bicubic interpolation (or Bilinear) that produces a blurred effect when images are enlarged. While Bicubic interpolation works great for normal images, pixel art scaled using Bicubic look terrible and blurry as hell. As as example ([source](http://blog.demofox.org/2015/08/15/resizing-images-with-bicubic-interpolation/)):

> Here’s the old man from The Legend of Zelda who gives you the sword. (*You may want to squint to see it*)

![old-man-from-zelda-original]({{ site.url }}/img/px-pixel/LozMan.bmp)

> Here he is scaled up 4x with nearest neighbor ... and bicubic interpolation.

![old-man-from-zelda-nn]({{ site.url }}/img/px-pixel/lozman_4_0.bmp)

![old-man-from-zelda-nn]({{ site.url }}/img/px-pixel/lozman_4_2.bmp)

So you'll need to tell Photoshop to use the 'Nearest Neighbor' image interpolation algorithm.

![photoshop-pixel-art]({{ site.url }}/img/px-pixel/5.png)

Note: If you are exporting the image ('Save for Web' option) and resizing it, make sure that 'Nearest neighbor' is selected under 'Quality' or 'Resample'.

## Step 3: Set up the Tools

You'll need to setup your drawing tools and get the desired pixelated effects. For pencil and eraser tools, here are the settings I used:

* size to 1 pixel (px).
* hardness to 100%.
* opacity to 100%.
* for the eraser tool, mode was set to 'Pencil'.

![photoshop-pixel-art]({{ site.url }}/img/px-pixel/2.png)

The only other tool I used was the paint bucket which didn't require any customization.

## Step 4: Show the Grid (optional)

Grid is helpful in positioning and aligning things precisely. I find grid very useful when creating sprites. Grid can be enabled from the 'View' menu.

![photoshop-pixel-art]({{ site.url }}/img/px-pixel/3.png)

Next, we'll need to **adjust the grid so it can display each pixel individually**. Open "Guides, Grids and Slices" settings from the Preferences menu and update the grid settings.

![photoshop-pixel-art]({{ site.url }}/img/px-pixel/4.png)

![photoshop-pixel-art]({{ site.url }}/img/px-pixel/5.png)

That's all there is. I hope you found this tutorial helpful and that you go on to create magnificent pixel art :) If you are looking for encouragement, to [quote](http://www.bobrossquotes.com/quotes.shtml) [Bob Ross](https://en.wikipedia.org/wiki/Bob_Ross):

> "**People might look at you a bit funny, but it's okay. Artists are allowed to be a bit different.**"

> “The secret to doing anything is believing that you can do it. **Anything that you believe you can do strong enough, you can do. Anything. As long as you believe.**”
