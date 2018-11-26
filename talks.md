---
layout: wide
title: Talks - Chris Keathley
---

<div class="hero-image" style="background-image: url(/assets/images/talks2.jpeg)">
    <header class="header">
      <h3>
        <a href="/" class="site-header">
          Keathley
        </a>
      </h3>
      <nav>
        <h4>
          <a href="/talks">Talks</a>
        </h4>
        <h4>
          <a href="https://github.com/keathley">Github</a>
        </h4>
        <h4>
          <a href="https://twitter.com/chriskeathley">Twitter</a>
        </h4>
        <h4 class="rss-link">
          <a href="http://keathley.github.io/feed.xml">RSS</a>
        </h4>
      </nav>
    </header>
  <div class="title-wrapper">
    <h1 class='title'>
      Talks
    </h1>
  </div>
</div>

<section class="content">
  I've been fortunate to get to travel around and share some of my ideas with people.

  <ul class="talks">
  {% for talk in site.talks %}
    <li class="talk">
      <span class="date">{{ talk.date | date: "%b %Y" }}</span>
      <h1 class="talk-title">
        <a href="{{ talk.url }}">{{ talk.title }}</a>
      </h1>
    </li>
  {% endfor %}
  </ul>
</section>

