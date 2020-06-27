---
layout: page
title: Projects
permalink: /projects/
---

__NOTE:__ that a lot of these projects are (almost) identical to what my instructor showed in the video tutorials. My contribution will be documenting how they work :D

{% for post in site.posts %}
* [{{ post.title }}]({{ post.url | prepend: site.baseurl }}): {{post.description}} --- certificate earned in {{ post.date | date: "%b %-d, %Y" }}
{% endfor %}
