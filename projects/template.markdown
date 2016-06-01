---
layout: page
title: "Project Name"
permalink: /projects/template.html
published: false
main_nav: false

project: true
project_description: "A short description of the project"
project_image: "/assets/images/placeholder.png"
project_date: "Month Year"
---

__Project Date:__ {{ page.project_date }}

<small>__Technologies:__ Language 1, Tool 2, Service 3</small>

![{{ page.project_description }}]({{ site.url }}{{ page.project_image }})

This is the template page for a project. A description of the project will go here. 

_Check out the [live demo][demo]! View the project on [GitHub][github]!_ 

[demo]: http://google.com
[github]: http://github.com