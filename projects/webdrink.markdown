---
layout: page
title: "WebDrink 2.0"
permalink: /projects/webdrink.html
published: true
main_nav: false

project: true
project_description: "A mobile-friendly web interface for CSH Drink"
project_image: "assets/images/webdrink/webdrink.png"
project_date: "Winter 2014"
---

__Project Date:__ {{ page.project_date }}

<small>__Technologies:__ HTML/CSS, AngularJS, PHP, MySQL, Twitter Bootstrap</small>

<a href="{{ page.project_image | prepend: site.baseurl }}" data-lightbox="webdrink" data-title="{{ page.project_description }}">
  <img src="{{ page.project_image | prepend: site.baseurl }}" title="{{ page.project_description }}">
</a>

[Computer Science House][csh]'s most famous project is without at doubt Drink, the networked vending machine that was once listed among the [10 Greatest Hacks of All Time][top10hacks]. The project has undergone several changes over the years, including the addition of two more machines (Little Drink and Snack) and multiple rewrites of the server component. The latest server rewrite (using Node.js) also brought the addition of a web interface dubbed "WebDrink", but it was in need of some updates. For my senior-year major project, I stepped up to the challenge and created WebDrink 2.0!

The main goal of WebDrink 2.0 was to provide an overall nicer experience. Using Twitter Bootstrap 3's responsive components I redesigned each page with mobile in mind to create an easy-to-use site for phones, tablets, and desktops alike. I paid special attention to the admin panel, since the Drink admins - those who manage inventory, distribute Drink credits, and maintain the machines - used WebDrink more than anyone else. This project was more than just a redesign; the entire site was written from the ground-up as a single-page web app powered by AngularJS.

I also decided to facilitate the creation of other Drink clients (mobile apps, etc) by creating a RESTful API for WebDrink. Written in PHP, the API handles all drink operations - getting machine stock, updating a user's credits, and more - except dropping, which is handled by WebDrink itself (using a websocket connection to the server). Authentication is provided by CSH's WebAuth, or you can pass an API key with each request after generating a key in WebDrink.

_Check out the [live demo][demo]!_ 

_View the project on [GitHub][github]!_ 

### Screenshots

Here are some side-by-side comparisons of WebDrink (left) to WebDrink 2.0 (right):

<a href="{{ site.baseurl }}assets/images/webdrink/comparison1.jpg" data-lightbox="webdrink" data-title="The main 'drop' page now shows all three machines at once">
  <img src="{{ site.baseurl }}assets/images/webdrink/comparison1.jpg" title="The main 'drop' page now shows all three machines at once">
</a>

<a href="{{ site.baseurl }}assets/images/webdrink/comparison2.jpg" data-lightbox="webdrink" data-title="The admin panel was a big focus of the redesign">
  <img src="{{ site.baseurl }}assets/images/webdrink/comparison2.jpg" title="The admin panel was a big focus of the redesign">
</a>

<a href="{{ site.baseurl }}assets/images/webdrink/comparison3.jpg" data-lightbox="webdrink" data-title="Of course, I kept the best parts of WebDrink in my redesign">
  <img src="{{ site.baseurl }}assets/images/webdrink/comparison3.jpg" title="Of course, I kept the best parts of WebDrink in my redesign">
</a>

<a href="{{ site.baseurl }}assets/images/webdrink/comparison4.jpg" data-lightbox="webdrink" data-title="Every page of WebDrink 2.0 is mobile-friendly">
  <img src="{{ site.baseurl }}assets/images/webdrink/comparison4.jpg" title="Every page of WebDrink 2.0 is mobile-friendly">
</a>

[demo]: http://bencentra.com/webdrink/
[github]: https://github.com/bencentra/WebDrink-2.0
[csh]: http://csh.rit.edu
[top10hacks]: http://www.pcmag.com/article2/0,2817,2330368,00.asp
