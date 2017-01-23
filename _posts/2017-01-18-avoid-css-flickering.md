---
layout: post
permalink: /avoid-css-flickering
title: Avoid CSS flickering
path: 2017-01-18-avoid-css-flickering.md
lang: en
ref: avoid-css-flickering
tags: [CSS]
---

> The purpose of this article is to focus on the most efficient way to avoid CSS flickering on HTML elements, especially with elements near a transitioned transformed element. The efficiency of the code provided in this article may vary depending on the kind and the version of your browser.

If you have several HTML elements that are nested into a common element (the parent) and you attached a transform effect with a transition on them, you probably ever seen a strange glitch on the direct succeeding elements when the transformation effect is triggered. This flickering effect is not comfortable for the user and there's several ways to fix that.

Here's an example of what a flickering effect is :
<video playsinline autoplay loop>
    <source src="./samples/css-flickering/css-flickering.webm" type="video/webm">
    <source src="./samples/css-flickering/css-flickering.mp4" type="video/mp4">
    Your browser does not support HTML5 video.
</video>

If you look closer at the above sample, you probably see the flicker effect on the other elements when the previous is hovered. For the last element, there's obviously no flicker effect since it doesn't have any following elements.

Fortunately, several options are available to fix that :

{% highlight css %}
-webkit-backface-visibility: hidden;
{% endhighlight %}

This CSS rule is designed to fix the flicker effect on Chrome-based browsers, applied to the HTML flickering element. But use it **wisely** : this rule works, but is not the most efficient way to avoid flickering effect. With this rule, you expose yourself to serious framerate drops and it can drastically reduce performance, so **use it sparingly on the troublesome elements**.

_Moreover, it will not work for sprites or image backgrounds._

So here is, ladies and gentleman the best way so far.
{% highlight css %}
-webkit-transform:translate3d(0,0,0);
{% endhighlight %}

Why is this rule better that the previous one ?

- Works on Safari and Chrome
- Works on sprites and image backgrounds
- Also works on SVGs
- Don't cause big framerate drops

Et voila! Here is the final result: 
<video playsinline autoplay loop>
    <source src="./samples/css-flickering/css-no-flickering.webm" type="video/webm">
    <source src="./samples/css-flickering/css-no-flickering.mp4" type="video/mp4">
    Your browser does not support HTML5 video.
</video>