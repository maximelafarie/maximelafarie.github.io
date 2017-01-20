---
layout: post
permalink: /click-event-on-highcharts-series-crosshair
title: Attach a click event on a Highcharts series crosshair
path: 2017-01-19-click-event-on-highcharts-series-crosshair.md
lang: en
ref: click-event-on-highcharts-series-crosshair
tags: [Javascript, Highcharts]
---

> [Highcharts](http://www.highcharts.com/) is a very powerful charting library written in pure JavaScript, offering an easy way of adding interactive charts to your web site or web application. Highcharts currently supports a lot of chart types such as line, spline, area, areaspline, column, bar, pie, scatter, angular gauges, arearange and so on.

### Preface

> If you don't already knew it, you can attach events on several Highcharts components like legend, tooltips, series and especially series points but not on a series **crosshair**. If you don't know charts series events you can learn more about it [here (line charts events)](http://api.highcharts.com/highcharts/series%3Cline%3E.data.events).

<iframe width="100%" height="467" src="//jsfiddle.net/maximelafarie/gmxpucud/embedded/result/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

To know what is a **series crosshair**, just hover the different columns of the above chart example. The highlight effect on the entire column is called a **crosshair**, and the column a **category**. 

So now you get the concept, the goal is to attach a **click** event on a series crosshair but it's not possible with the actual Highcharts API so we need to make it ourselves.


### Step 1: What do we really want

Firstly, we need to define what we want to do: easy you say ? You're right! Highchars comes with its own utility called `wrap`, which wraps an existing prototype method and allows you to add your own code before or after it.  So the solution is to [extend Highcharts](http://www.highcharts.com/docs/extending-highcharts/extending-highcharts)'s `drawCrosshair` method and add our own event function!

### Step 2: Let's code it!

Now we know how to <a id="link" href="//youtu.be/ZXsQAXx_ao0?t=3s" target="_blank">do it<sup>*</sup></a>, let's dev a little snippet: 

{% highlight javascript %} 
(function(H) {
    H.wrap(H.Axis.prototype, 'drawCrosshair', function(proceed) {
        var axis = this;
        wasRendered = !!axis.cross;
        proceed.apply(axis, Array.prototype.slice.call(arguments, 1));

        if (!wasRendered && axis.cross && axis.options.clickOnCrosshair) {
            axis.cross.on('click', function(e) {
                var point = axis.chart.hoverPoint;
                axis.options.clickOnCrosshair(e, point);
            });
        }

    });
})(Highcharts);
{% endhighlight %}

Some explanations about the above code : 

> The `wrap` function accepts the `parent` object as the first argument, the `name` of the function to wrap as the second, and a `callback replacement function` as the third. The original function is passed as the first argument to the replacement function, and original arguments follow after that.

Given that a crosshair is associated to an axis (yAxis so a category here), we need to add the chrosshair click event on the Axis Highcharts property to make it available for all Highcharts instances. So pass `H.Axis.prototype` as first argument to tell Highcharts you want to modify a prototype of the Axis property of the Highcharts constructor.

Then we need to tell the `wrap` method which prototype of `Highcharts.Axis` we want to override. `drawCrosshair` is an existing method designed to draw the crosshair `canvas` on a chart category (axis). Since this method already exists and as said above, the `drawCrosshair` will be replaced by the code written into the _replacement function_ (passed as third argument of the `wrap` method) and passed as first argument of this _replacement function_ in order to improve it (because we doesn't want to clear all the code but improve it by the piece of code given).

Finally, the _replacement function_ is passed as third argument of the `wrap` method with an argument (named `proceed` here) that is equal to the original `Highcharts.Axis.drawCrosshair` method.

So in the above code (inside the _replacement function_) `axis` is an alias to `this` which refers to the current Axis instance (a chart could have several axises (categories), so it creates several instances).

> The `apply()` method calls a function with a given `this` value and `arguments` provided as an array (or an array-like object).

{% highlight javascript %}
proceed.apply(axis, Array.prototype.slice.call(arguments, 1));
{% endhighlight %}

The above code executes `proceed` witch is the equivalent to `Highcharts.Axis.drawCrosshair` so for the moment, nothing really change compared to the original native method.

{% highlight javascript %} 
if (!wasRendered && axis.cross && axis.options.clickOnCrosshair) {
    axis.cross.on('click', function(e) {
        var point = axis.chart.hoverPoint;
        axis.options.clickOnCrosshair(e, point);
    });
}
{% endhighlight %}

And here's the code responsible of our crosshair click event! 
What's important is to check if `axis.cross` is defined because it's not on init and it globally represents our series crosshair. We're also looking for a `clickOnCrosshair` object with a callback method inside the chart configuration object.

If all conditions are satisfied, then we call the callback function given in the chart options object with the click event and the hovered point's data as arguments.

{% highlight javascript %} 
clickOnCrosshair: function(e, p) {
    alert('Click on ' + p.category + ' category!')
    console.log(this, e, p);
}
{% endhighlight %}

You simply need to set the above code into the xAxis object in your chart options, and set whatever actions in the callback functions. In the example it shows the name of the category and it logs the axis instance, the click event and the hovered point data.

Here is the final result :
<iframe width="100%" height="467" src="//jsfiddle.net/maximelafarie/gmxpucud/1/embedded/result,js,html/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

I hope you understand everything. If not, feel free to ask in the comments!

<sub><sup>*</sup>_I'm sorry. I couldn't resist!_</sub> <a href="#link">↑</a>
<div><img src="img/gif/django_couldnt_resist.gif"></div>