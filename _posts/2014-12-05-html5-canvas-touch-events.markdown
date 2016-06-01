---
layout: post
title:  "Using Touch Events with the HTML5 Canvas"
author: Ben Centra
date: 2014-12-05 11:00:00
categories: code
tags: touch events html5 canvas javascript
---

![A mobile-friendly "e-signature" made using the HTML5 canvas]({{ site.url }}/assets/images/esignature-screenshot.png)

The HTML5 canvas element has been around for a while now, and it’s great for lots of things: drawing, games, user input, and more. It’s also fairly easy to use, and its API is similar to other drawing APIs out there. What’s not so easy is getting the canvas to work with both mouse and touch events, a requirement for mobile-friendly applications.

Partially as a joke I wanted to add an “e-signature” feature to a [friend’s project][cshpacket]. Since it was meant to be mobile-friendly, it was time to use the canvas with touch support! Here's how it was done:

_You can check out the final demo [here][demo] and view the code [here][code]._

### Canvas Setup

Canvas setup is easy enough:

{% highlight html %}
<canvas id="sig-canvas" width="320" height="160">
Get a better browser, bro.
</canvas>
{% endhighlight %}

{% highlight javascript %}
// Set up the canvas
var canvas = document.getElementById("sig-canvas");
var ctx = canvas.getContext("2d");
ctx.strokeStyle = "#222222";
ctx.lineWith = 2;
{% endhighlight %}

### Mouse Input

In order to draw a signature, I needed to capture user input on the canvas. Starting with mouse input, I handled these three mouse events:

1. `mousedown` - to toggle drawing mode “on”
2. `mouseup` - to toggle drawing mode “off”
3. `mousemove` - to toggle mouse position, used in drawing

{% highlight javascript %}
// Set up mouse events for drawing
var drawing = false;
var mousePos = { x:0, y:0 };
var lastPos = mousePos;
canvas.addEventListener("mousedown", function (e) {
        drawing = true;
  lastPos = getMousePos(canvas, e);
}, false);
canvas.addEventListener("mouseup", function (e) {
  drawing = false;
}, false);
canvas.addEventListener("mousemove", function (e) {
  mousePos = getMousePos(canvas, e);
}, false);

// Get the position of the mouse relative to the canvas
function getMousePos(canvasDom, mouseEvent) {
  var rect = canvasDom.getBoundingClientRect();
  return {
    x: mouseEvent.clientX - rect.left,
    y: mouseEvent.clientY - rect.top
  };
}
{% endhighlight %}

### Drawing

Now that I knew the state of the mouse, I could start drawing to the canvas. To make it happen smoothly and efficiently, I took advantage of the browser method `requestAnimationFrame`. I used a handy function that determines the appropriate method of getting the window’s animation frame or, failing that, falling back on a simple timeout loop:

{% highlight javascript %}
// Get a regular interval for drawing to the screen
window.requestAnimFrame = (function (callback) {
        return window.requestAnimationFrame || 
           window.webkitRequestAnimationFrame ||
           window.mozRequestAnimationFrame ||
           window.oRequestAnimationFrame ||
           window.msRequestAnimaitonFrame ||
           function (callback) {
        window.setTimeout(callback, 1000/60);
           };
})();
{% endhighlight %}

To actually do the drawing, I set up a draw loop. I created a `renderCanvas()` function for drawing the signature, connecting the previous and current mouse positions with a line (if drawing is enabled). By hooking into the window’s animation frame and running that function in a loop, I got a fully interactive signature field!

{% highlight javascript %}
// Draw to the canvas
function renderCanvas() {
  if (drawing) {
    ctx.moveTo(lastPos.x, lastPos.y);
    ctx.lineTo(mousePos.x, mousePos.y);
    ctx.stroke();
    lastPos = mousePos;
  }
}

// Allow for animation
(function drawLoop () {
  requestAnimFrame(drawLoop);
  renderCanvas();
})();
{% endhighlight %}

### Touch Input

Since the project to which I was supposedly contributing was a modern web app, I needed to support smartphones and tablets. This meant adding touch controls to supplement the mouse controls. Keeping it simple, I only used one touch at a time (sorry, multitouch). For starters, I utilized three touch event counterparts to the mouse events from earlier:

1. `touchstart` – to toggle drawing mode “on”
2. `touchend` – to toggle drawing mode “off”
3. `touchmove` – to track finger position, used in drawing

Because I wanted to play around with event dispatching, I used these touch events to trigger their mouse event counterparts and do the appropriate conversions (touch position to mouse position, etc).

{% highlight javascript %}
// Set up touch events for mobile, etc
canvas.addEventListener("touchstart", function (e) {
        mousePos = getTouchPos(canvas, e);
  var touch = e.touches[0];
  var mouseEvent = new MouseEvent("mousedown", {
    clientX: touch.clientX,
    clientY: touch.clientY
  });
  canvas.dispatchEvent(mouseEvent);
}, false);
canvas.addEventListener("touchend", function (e) {
  var mouseEvent = new MouseEvent("mouseup", {});
  canvas.dispatchEvent(mouseEvent);
}, false);
canvas.addEventListener("touchmove", function (e) {
  var touch = e.touches[0];
  var mouseEvent = new MouseEvent("mousemove", {
    clientX: touch.clientX,
    clientY: touch.clientY
  });
  canvas.dispatchEvent(mouseEvent);
}, false);

// Get the position of a touch relative to the canvas
function getTouchPos(canvasDom, touchEvent) {
  var rect = canvasDom.getBoundingClientRect();
  return {
    x: touchEvent.touches[0].clientX - rect.left,
    y: touchEvent.touches[0].clientY - rect.top
  };
}
{% endhighlight %}

Unfortunately, all was not well. An issue arose from a conflict with built-in browser gestures. When I moved my finger on the canvas, I wanted the page to stay still so I could draw. However, if my page had horizontal or vertical scrolling (and it did) the page would move along with my finger and making proper drawing impossible. After some frustration, I stumbled upon the solution: preventing scrolling on document.body if the target of a touch event is the canvas.

{% highlight javascript %}
// Prevent scrolling when touching the canvas
document.body.addEventListener("touchstart", function (e) {
  if (e.target == canvas) {
    e.preventDefault();
  }
}, false);
document.body.addEventListener("touchend", function (e) {
  if (e.target == canvas) {
    e.preventDefault();
  }
}, false);
document.body.addEventListener("touchmove", function (e) {
  if (e.target == canvas) {
    e.preventDefault();
  }
}, false);
{% endhighlight %}

And viola, a mobile-friendly e-signature!

### Bonus: Save the Canvas as a Data URL

Now that I had a signature (or doodle, or whatever) it would make sense to save it somewhere. The cheap and easy solution is to save the contents of the canvas directly as a 64-bit data URL, and here’s how:

{% highlight javascript %}
var dataUrl = canvas.toDataURL();
{% endhighlight %}

You can then easily store the data URL in a database, set it to the `src` attribute of an image element, etc.

### Bonus: Clearing the Canvas

I don’t exactly know why (though I would love to know), but using `canvas.clearRect()` or `canvas.fillRect()` to clear or cover the canvas in white, respectively, didn’t actually clear the canvas. The next time I went to draw, the signature I thought I deleted would come back! The unintuitive solution is to reset the canvas’ width, which completely resets the canvas and its context.

{% highlight javascript %}
function clearCanvas() {
    canvas.width = canvas.width;
}
{% endhighlight %}

### Next Step: Scalable Canvas

The one thing my e-signature demo doesn’t do is scale the canvas based on the window size. As far as I know, the canvas needs to have a fixed height and width, which rules out media queries and CSS. I could hook into the window.resize event and do it through JavaScript, but that didn’t seem like a great solution. There was also the question: do I want scaling to cause a variety of image sizes since the canvas size will change, or am I just lazy and don’t want to do it? If you have any advice (aside from “don’t be lazy”), please let me know!

### Resources:

* [HTML5 Canvas (w3schools)][w3schools]
* [requestAnimationFrame for Smart Animating][paulirish]
* [Touch Events (MDN)][mdn]
* [HTML5 Canvas Get Image Data URL][dataurl]
* [How to clear the canvas for redrawing][clearcanvas]

[cshpacket]: https://github.com/TalCohen/CSHWebPacket
[demo]: http://bencentra.com/canvas/signature/signature.html
[code]: https://github.com/bencentra/canvas/tree/master/signature
[w3schools]: http://www.w3schools.com/html/html5_canvas.asp
[paulirish]: http://www.paulirish.com/2011/requestanimationframe-for-smart-animating/
[html5rocks]: http://www.html5rocks.com/en/mobile/touch/
[mdn]: https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Touch_events
[dataurl]: http://www.html5canvastutorials.com/advanced/html5-canvas-get-image-data-url/
[clearcanvas]: http://stackoverflow.com/questions/2142535/how-to-clear-the-canvas-for-redrawing


