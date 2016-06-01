---
layout: page
title: "Reversi"
permalink: /projects/reversi.html
published: true
main_nav: false

project: true
project_description: "An online game of Reversi"
project_image: "assets/images/reversi_game2.png"
project_date: "Spring 2013"
---

__Project Date:__ {{ page.project_date }}

<small>__Technologies:__ HTML/CSS, JavaScript, PHP, MySQL, SVG</small>

<a href="{{ page.project_image | prepend: site.baseurl }}" data-lightbox="reversi" data-title="{{ page.project_description }}">
  <img src="{{ page.project_image | prepend: site.baseurl }}" title="{{ page.project_description }}">
</a>

For the final project in "Web Client-Server Programming" we had to create an online game, and I chose Reversi. First I whipped up a prototype in Flash to get a better understanding of the game itself. You can play it [here][prototype].

Then I set to work real project: a MySQL database for storing game and user data; a simple sevice-oriented architecture written in PHP to handle game moves, chat messages, and other actions; the actual Reversi game powered by JavaScript and SVG; and the entire website, made using Twitter Bootstrap. Users can create an account, chat in the lobby, challenge other users, and play a game of Reversi online.

_Check out the [live demo][demo]!_

[demo]: http://bencentra.com/reversi/
[prototype]: http://bencentra.com/portfolio/reversi.swf
