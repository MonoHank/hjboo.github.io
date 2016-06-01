---
layout: post
title:  "Optimizing window.onresize"
author: Ben Centra
date:   2015-02-27 20:00:00
categories: code
tags: window resize javascript debounce throttle
---

Let's say you're working on a webpage. You want something on that webpage to respond to changing the size of the window, in a way that can't be handled with straight CSS or [media queries][mediaqueries]. So you take advantage of the [window resize event][windowresize], attach an event handler to do your thing, and all seems to be good.

However, directly hooking into window.onresize isn't very performant. Each small change made to the window size will fire a separate resize event. If you're performing an expensive operation each resize, your page will start to lag and the user experience will suffer. At the very least, it may be confusing or unnecessary to react to every little change.

Luckily there are two ways to improve the performance of window.onresize: __throttling__ and __debouncing__.

### No Optimization

Here's a [contrived example](http://codepen.io/bencentra/full/yyEMMY/) with no optimization. Try resizing the window; every small change in the window size triggers the event handler.

<div data-height="268" data-theme-id="12659" data-slug-hash="yyEMMY" data-default-tab="js" data-user="bencentra" class='codepen'>
<p>See the Pen <a href='http://codepen.io/bencentra/pen/yyEMMY/'>Regular window.resize Example</a> by Ben Centra (<a href='http://codepen.io/bencentra'>@bencentra</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
</div><script async src="//assets.codepen.io/assets/embed/ei.js"></script>
<br>

### Throttling

[Throttling][throttle] window.onresize limits how often the event handler will be called by setting a timeout between calls. This will allow you to call the function at a reasonable rate.

A [basic example](http://codepen.io/bencentra/full/ogyZBp/) looks like this:

<div data-height="266" data-theme-id="12659" data-slug-hash="ogyZBp" data-default-tab="js" data-user="bencentra" class='codepen'>
<p>See the Pen <a href='http://codepen.io/bencentra/pen/ogyZBp/'>Throttling Example</a> by Ben Centra (<a href='http://codepen.io/bencentra'>@bencentra</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
</div><script async src="//assets.codepen.io/assets/embed/ei.js"></script>
<br>

### Debouncing 

[Debouncing][debounce] window.onresize will only call the event handler after the event has stopped firing for a certain amount of time. This will ensure that your function will only be called once the resizing is "complete."

A [basic example](http://codepen.io/bencentra/full/PwapWX/) looks like this:

<div data-height="266" data-theme-id="12659" data-slug-hash="PwapWX" data-default-tab="js" data-user="bencentra" class='codepen'>
<p>See the Pen <a href='http://codepen.io/bencentra/pen/PwapWX/'>Debouncing Example</a> by Ben Centra (<a href='http://codepen.io/bencentra'>@bencentra</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
</div><script async src="//assets.codepen.io/assets/embed/ei.js"></script>
<br>

Throttling and debouncing can be applied to other JavaScript events as well. If you don't want to implement them yourself, [several](http://devdocs.io/lodash/index#throttle) [libraries](http://devdocs.io/underscore/index#debounce) have implementations and [many](https://code.google.com/p/jquery-debounce/) [plugins](https://github.com/cowboy/jquery-throttle-debounce) already exist.

[mediaqueries]: https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries
[windowresize]: https://developer.mozilla.org/en-US/docs/Web/API/Window/onresize
[throttle]: http://sampsonblog.com/749/simple-throttle-function
[debounce]: https://remysharp.com/2010/07/21/throttling-function-calls
