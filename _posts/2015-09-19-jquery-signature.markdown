---
layout: post
title:  "jQuery Signature Plugin"
categories: projects
author: Ben Centra
date: 2015-09-19 18:00:00
tags: projects jquery signature plugin
---

A while back I wrote about [touch events and the HTML5 canvas][post]. In writing that post I created a demo of a "signature" field for a web page that could be used on both desktop and mobile browsers. Recently I revisited the e-signature itself and turned it into a standalone jQuery plugin. 

Check out the [demo page][demo] and the [source code][github] on GitHub. It's registered as a [Bower package][bower], too!

### Update: Clearing the Canvas

Aside from restructuring it as a jQuery plugin, the code remains about the same. I added a few initialization parameters for customizing the signature field - size, color, etc - and fixed one small bug: in Internet Explorer (and Edge), the canvas wouldn't actually clear when calling the `clearCanvas` method. This was because I was using a hacky way of clearing a canvas element:

{% highlight javascript %}
function clearCanvas() {
  canvas.width = canvas.width;
}
{% endhighlight %}

This code is bad for [several reasons][stackoverflow]: it can be slow, it is broken in the IE family of browsers, and (without the method name) it is unclear what is actually happening. The solution is to use the `clearRect()` method of the canvas API, and to render lines properly by calling `beginPath()` before drawing:

{% highlight javascript %}
// Clear the canvas
clearCanvas: function() {
  this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
  this._resetCanvas();
},

// Render the signature to the canvas
_renderCanvas: function() {
  if (this.drawing) {
    this.ctx.beginPath();
    this.ctx.moveTo(this.lastPos.x, this.lastPos.y);
    this.ctx.lineTo(this.currentPos.x, this.currentPos.y);
    this.ctx.stroke();
    this.lastPos = this.currentPos;
  }
},
{% endhighlight %}

[post]: http://bencentra.com/code/2014/12/05/html5-canvas-touch-events.html
[github]: https://github.com/bencentra/jq-signature
[demo]: http://bencentra.github.io/jq-signature/
[bower]: http://bower.io/search/?q=jq-signature
[stackoverflow]: http://stackoverflow.com/questions/2142535/how-to-clear-the-canvas-for-redrawing