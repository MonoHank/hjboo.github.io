---
layout: page
title: "EEGJ"
permalink: /projects/eegj.html
published: true
main_nav: false

project: true
project_description: "An Immersive, Brain-Powered Audio/Visual Experience"
project_image: "assets/images/eegj_antwan.jpg"
project_date: "Spring 2014"
---

__Project Date:__ {{ page.project_date }}

<small>__Technologies:__ Processing, MIDI, Arduino, Ableton Live</small>

<a href="{{ page.project_image | prepend: site.baseurl }}" data-lightbox="eegj_antwan" data-title="{{ page.project_description }}">
  <img src="{{ page.project_image | prepend: site.baseurl }}" title="{{ page.project_description }}">
</a>

My final semester at RIT was consumed by New Media Team Project. I served as the development lead on a team of four developers and three designers tasked with making something... awesome. Our group quickly meshed on a general idea: create an interactive experience with generative audio and retro visuals, powered by your brain! After 15 long weeks the result was [EEGJ][eegj], or "Electroencephalogram Jockey," which was a hit at Imagine RIT 2014.

<iframe src="//player.vimeo.com/video/93481299" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe> 
<p><a href="http://vimeo.com/93481299">EEGJ</a> from <a href="http://vimeo.com/user5359795">Whitney Brown</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

The exhibit used a [hacked MindFlex toy][mindflex-hack] as the primary input and three projectors powered our display. Users sat in front of the display and donned the MindFlex, which provided a rough reading of their brain waves. This brain data was used to control a Guitar Hero-esque game and determine the tone, tempo, and instrumentation of a generative music track. Pressure sensors built into the chair were used to countrol audio filters and visual "turntables". The game took up the center display, and the side displays included other visuals that beat, pulsed, and moved in time with the tempo of the music.

<iframe src="//player.vimeo.com/video/95632292" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe> 
<p><a href="http://vimeo.com/95632292">EEGJ - Imagine R.I.T.</a> from <a href="http://vimeo.com/creativerussell">Antwan Russell</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

My main contribution to the project was the ["Riri Framework"][riri-framework]. Named for our project's early title ("Riri," a nonsense word), the Riri Framework was a Processing library that allows you to create pre-programmed or generative sequences of MIDI data. The framework consisted of RiriNotes (individual MIDI notes) and RiriChords (groups of notes to be played simultaneously) that could be strung together into RiriSequences and played indefinitely. Using another Processing libary called [The MidiBus][midibus], you can send the MIDI data generated with the Riri Framework to any other program (in our case Ableton Live) to be turned into sound using soundbanks or sofware instruments.

<iframe width="100%" height="166" scrolling="no" frameborder="no" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/146754815&amp;color=ff5500&amp;auto_play=false&amp;hide_related=false&amp;show_comments=true&amp;show_user=true&amp;show_reposts=false"></iframe>
<p></p>

In addition to the Riri Framework, I was responsible for organizing and distributing work amongst the other developers. I also did early experiments with MIDI for the sound and Arduino for the various sensors, as well as assembled all of the individual components into the final Processing sketch used at the exhibit.

_View the project on [GitHub][github]!_

[eegj]: http://eegj.me/
[riri-framework]: https://github.com/codenameriri/riri-framework
[mindflex-hack]: http://frontiernerds.com/brain-hack
[github]: https://github.com/codenameriri
[midibus]: http://www.smallbutdigital.com/themidibus.php
