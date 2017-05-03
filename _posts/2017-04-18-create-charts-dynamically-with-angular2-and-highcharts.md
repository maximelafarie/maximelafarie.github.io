---
layout: post
permalink: /create-charts-dynamically-with-angular2-and-highcharts
title: Create charts dynamically with Angular 2 and Highcharts
path: 2017-04-18-create-charts-dynamically-with-angular2-and-highcharts.md
lang: en
ref: create-charts-dynamically-with-angular2-and-highcharts
tags: [Angular 2, Highcharts]
---

> A recurrent question is "**How to create charts dynamically?**". So here's an article to treat this subject **without any external package**. As previous Angular 2 articles, I'm using **TypeScript** and **ES5**. Yes, I know Angular (4.x) is released but both versions slightly works the same. The below code could be optimized but I want to show you all the steps to reach our target! Of course, if you have any questions, feel free to ask me in comments!

At first we need to define exactly what we need and how we are going to code it. The goal is to define what we really need to dev this functionality without useless stuff. So let's form the basis of our app. 

Here's the main tree of the app (obviously it's a Plunker app so the tree may differ yours):
{% highlight javascript %}
├── app
│   ├── _services
│   │   ├── highcharts.service.ts
│   │   └── index.ts
│   ├── app.component.ts
│   ├── app.module.ts
│   └── main.ts
├── index.html
├── systemjs.config.js
└── tsconfig.json
{% endhighlight %}

> As you can see above there are essential things like all the `app.*` files, vital for the Angular application. The most important is the `_services` folder that contains an `index.ts` file (to facilitate all import statements) and `highcharts.service.ts`, **the** file that will do the work!

Let's code! Here's our `highcharts.service.ts`:
{% highlight javascript %}
import { Injectable } from '@angular/core';
const Highcharts = require('highcharts/highcharts.src');
import 'highcharts/adapters/standalone-framework.src';
  
@Injectable()
export class HighchartsService {

  charts = [];
  defaultOptions = {
    /* Your Highcharts options here... */
  }

  constructor() {
  }
  
}
{% endhighlight %}

The above code is the basis of our service: it will allow us to create, store, retrieve and remove **Highcharts instances**.
We import `Highcharts` in order to create some cool charts. The `charts = [];` variable will contain all the chart instances. `defaultOptions = {};` is a default option object in case of no custom configuration is given at the **Highcharts** creation time.

Let's talk about the rest of the code into this service:
{% highlight javascript %}
 createChart(container, options?: Object) {
    let opts = !!options ? options : this.defaultOptions;
    let e = document.createElement("div");
    
    container.appendChild(e);
    
    if(!!opts.chart) {
      opts.chart['renderTo'] = e;
    }
    else {
      opts.chart = {
        'renderTo': e
      }
    }
    this.charts.push(new Highcharts.Chart(opts));
  }
  
  removeFirst() {
    this.charts.shift();
  }
  
  removeLast() {
    this.charts.pop();
  }
  
  getChartInstances(): number {
    return this.charts.length;
  }

  getCharts() {
    return this.charts;
  }
{% endhighlight %}

Wow, things happened at the top! Let's check it out:
the `createChart(container, options?: Object)` method creates Highcharts instances and automatically push them to the DOM (and our array previously defined). It takes a `container` parameter that is a **Node element**, in order to give Highcharts a place to append its chart.
It also takes a second optional parameter `options?` (please note the `?` after the parameter declaration to make it optional) which will eventually takes an Object. Refer to the [Highcharts's official documentation](//api.highcharts.com/highcharts/Highcharts.chart) to make your own chart options.

Inside our `createChart(...)` function, we declare two variables: `opts` that will contain the given chart options passed to the function (if no options are given, it takes a default configuration **Object** `this.defaultOptions`).

The `e` variable ("e" for "element") is quite simple to understand: it contains a new **DOM element** to tell our new **Highcharts instance** a place to append all the chart code (to sum up, a big **SVG** element). If you want to learn how to manipulate **Nodes**, then [check this doc out](//www.w3schools.com/js/js_htmldom_nodes.asp)!

The `if() { ... } else { ... }` statement that follows is designed to set the `renderTo` property of the `opts` **Object**. If the passed `options` object doesn't contain the `chart` property (which includes the `renderTo` prop.) then we create it and assign it to our freshly created **Node**: `e`.

Finally we only need to push into the `charts` array a new instance of an awesome Highcharts chart! 

That's all for this step!

> Don't forget to declare the `HighchartsService` into the `app.module.ts` providers!

From now, the hardest is done! Let's take a look at the `app.component.ts`.

Firstly we need to import our awesome service:
{% highlight javascript %}
import {HighchartsService} from './_services/index';
{% endhighlight %}
So we can use it in the component's **constructor**:
{% highlight javascript %}
constructor(private hcs: HighchartsService) {}
{% endhighlight %}

Then we declare two different functions to create some cool charts using the `HighchartsService`
{% highlight javascript %}
createChart() {
  this.hcs.createChart(this.chartEl.nativeElement);
}

createCustomChart(myOpts: Object) {
  this.hcs.createChart(this.chartEl.nativeElement, myOpts);
}
{% endhighlight %}

To manipulate our charts easily from the view, we also declare two functions to remove the first or the last charts (you can obviously customize them to fit your needs)
{% highlight javascript %}
rmFirst() {
  this.hcs.removeFirst();
  this.changeDetectionRef.detectChanges();
  if (!!document.getElementById("test").firstChild) document.getElementById("test").firstChild.outerHTML = '';
  console.log('rm first', this.hcs.getCharts());
}

rmLast() {
  this.hcs.removeLast();
  this.changeDetectionRef.detectChanges();
  if (!!document.getElementById("test").lastChild) document.getElementById("test").lastChild.outerHTML = '';
  console.log('rm last', this.hcs.getCharts());
}
{% endhighlight %}

Now let's use these functions into our view
{% highlight javascript %}
@Component({
    selector: 'my-app',
    template: `
        <button (click)="createChart()">Let's create a new chart !</button>
        <button (click)="createCustomChart(myCustomOptions)">Let's create a custom chart !</button>
        <button (click)="rmFirst()">Remove top chart</button>
        <button (click)="rmLast()">Remove last chart</button>
        <div #charts id="test"></div>
    `
})
{% endhighlight %}

> Notice the `#charts` variable! We're going to use it in our component to give Highcharts's charts a **Node** parent to append its code (as said above, a big SVG element).

And finally, don't forget to put this at the top of the component's **Class**:
{% highlight javascript %}
@ViewChild('charts') public chartEl: ElementRef;
{% endhighlight %}

Here is the final result! Don't hesitate to play with it.
<iframe style="width: 100%; height: 520px" src="//embed.plnkr.co/uO8BLf2ZLDl89RlkTZr9?show=preview" frameborder="0" allowfullscren="allowfullscren"></iframe>

Feel free to ask any questions you may have!
