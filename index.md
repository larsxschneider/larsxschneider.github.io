---
layout: page
title: Software Engineering.
---
{% include JB/setup %}

Lars is a passionate software engineer, a perfectionist in software architecture, yet pragmatic with implementation. He programs with <a href="http://pragdave.me/blog/2014/03/04/time-to-kill-agile/">agility</a> and lives in Berlin, Germany.

You might want to follow Lars on <a href="https://twitter.com/kit3bus">Twitter</a> or read his code on <a href="https://github.com/larsxschneider">GitHub</a>. Send him <a
    href="mailto:&#108;&#97;&#114;&#115;&#120;&#115;&#99;&#104;&#110;&#101;&#105;&#100;&#101;&#114;&#43;&#98;&#108;&#111;&#103;&#64;&#103;&#109;&#97;&#105;&#108;&#46;&#99;&#111;&#109;">an email</a> to get in touch.

<br>

## Posts
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
