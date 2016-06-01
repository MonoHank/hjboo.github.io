---
layout: post
title:  "Dual 'data-toggle' Attribute Workaround with Twitter Bootstrap Components"
author: Ben Centra
date:   2014-10-22 16:30:00
categories: code
tags: html css bootstrap data-toggle workaround
---

Many of [Twitter Bootstrap][bootstrap]'s JavaScript components rely on HTML `data` attributes for configuration or triggering. For example, the [Modal][modals] component requires you set `data-toggle="modal"` on the element, usually a button, that will trigger the modal being displayed. This is all well and good until you want to have one HTML element be the trigger for two different components. 

For example, I had a `<button>` that triggered a Modal. I also wanted to apply a [Popover][popover] to the `<button>` to act as a notification. This presented a problem because you can't apply multiple `data-toggle` attributes to an element. You also (currently) can't give it multiple values, such as `data-toggle="modal popover"`. However, at least for this example, I was able to find a workaround. 

First, I setup a `<button>` to trigger the Modal. Next, I wrapped the `<button>` in a `<span>` that would be the target of the Popover. This way, the popover would appear under the `<button>`, as desired, while still allowing the Modal to be triggered. Lastly, I added a bit of JavaScript to show the popover by default, allowing for the desired notification behavior.

Here's an example in action:

<iframe src="{{ site.url }}/portfolio/bootstrap_multiple_toggle.html" width="320" height="240"></iframe>
<br/>

[bootstrap]: http://getbootstrap.com/
[modals]: http://getbootstrap.com/javascript/#modals
[popover]: http://getbootstrap.com/javascript/#popovers
[demo]: http://bencentra.com/portfolio/bootstrap_multiple_toggle.html
