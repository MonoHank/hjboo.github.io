---
layout: page
title: "Kinect Stuff" 
permalink: /projects/kinect.html
published: true
main_nav: false

project: true
project_description: "A variety of Kinect-based projects"
project_image: "assets/images/kinecthelper.jpg"
project_date: "Winter 2012"
---

__Project Date:__ {{ page.project_date }}

<small>__Technologies:__ Kinect, C#</small>

<a href="{{ page.project_image | prepend: site.baseurl }}" data-lightbox="kinect" data-title="{{ page.project_description }}">
  <img src="{{ page.project_image | prepend: site.baseurl }}" title="{{ page.project_description }}">
</a>

Back in my freshman year at RIT, I managed to get my hands on a [Microsoft Kinect][kinect] and cobble together a motion-controlled game of Pong. It functioned, but it was a mess. Two years later, I decided I was ready to tackle a serious Kinect project. I began by writing a C# library that wrapped the Kinect SDK, providing simpler setup and easy access to the Kinect's data streams - color video, depth data, skeleton tracking, and audio input - for use in WPF applications. Included with the library is a demo application, pictured above. I then used used the library in two other projects that were featured in the 2013 Imagine RIT festival.

_View the project on [GitHub][helper]!_

<a href="{{ site.baseurl }}assets/images/kinecttheremin.jpg" data-lightbox="kinect" data-title="A theremin-esque musical instrument powered by Kinect">
  <img src="{{ site.baseurl }}assets/images/kinecttheremin.jpg" title="A theremin-esque musical instrument powered by Kinect">
</a>

The first project was a theremin-esque musical instrument. The user stood in front of the Kinect and used their hands to control the volume and pitch of a sine wave. The right hand controlled the frequency (pitch), and the left hand controlled the amplitude (volume). "Guides" could be enabled to play individual notes, or disabled to glide between all pitches. The sine wave was dynamically generated using the [NAudio][naudio] library.

_View the project on [GitHub][theremin]!_

<!-- ![A motion-controlled multiplayer game of Pong]({{ site.url }}/assets/images/kinectpong.jpg) -->
<a href="{{ site.baseurl }}assets/images/kinectpong.jpg" data-lightbox="kinect" data-title="A motion-controlled multiplayer game of Pong">
  <img src="{{ site.baseurl }}assets/images/kinectpong.jpg" title="A motion-controlled multiplayer game of Pong">
</a>

The second project was a revamped version of Kinect Pong. Two players stood side-by-side in front of the Kinect, each controlling a paddle with their hands. Each player was tracked independently, so they couldn't interfere with the other. 

_View the project on [GitHub][pong]!_

[kinect]: http://www.microsoft.com/en-us/kinectforwindows/
[helper]: https://github.com/bencentra/WpfKinectHelper
[theremin]: https://github.com/bencentra/kinect-theremin
[pong]: https://github.com/bencentra/kinect-pong
[naudio]: http://naudio.codeplex.com/
