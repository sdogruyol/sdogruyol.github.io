---
layout: post
status: publish
published: true
title: 'Rails vs Sails : A Quick And Dirty Benchmark'
date: '2013-09-03 12:03:13 +0300'
categories:
- javascript
tags:
- rails
- node.js
- sails.js
- benchmark
redirect_from: "/?p=111"
comments: []
---

As a developer who’s mostly involved in Ruby and JS ( Node ) development i’m pretty interested in what’s new / hot in those. I love Ruby and i really like using Rails and the convention over configuration motto. I also love Javascript ( especially CoffeeScript ) and use Node in small / medium sized apps in production. Even though Node is pretty fast and easy to start with it’s easy to get lost on the road and also really easy to make technical design mistakes due to both JS and Node ( Pyramid of Doom , Promise , Fiber e.g anyone? ).

From the start I’ve always been keeping eyes on Node.js frameworks ( Flatiron, Geddy, Zappa, Meteor e.g) but none of them seemed attractive and exciting to me. Maybe it’s because i was spoiled by the Rails magic who knows :) . If i remember correctly around 6 months ago i saw something like this on Github or so ‘Sails.js : Rails way in Node ‘ . That really picked my interest and i quickly installed did some ‘Hello world’ stuff and that’s it. It looked ok to me but didn’t get my full attention.

Now 6 months later i gave Sails another try and checked it back. I can easily say that Sails pretty impressed me it’s like i can feel the Rails aura in it. Maybe not now but on my next projects i can start a real project on it.

As i consider using it for real world stuff i did a quick benchmark by creating a very simple similar apps on both Rails and Sails.

## Benchmarks

The first benchmark is pretty simple. Rendering a static page containing Hello World text.

First off Rails

<img src="/images/rails_static.png" width="300" height="300"/>

Sails.js

<img src="/images/sails_static.png" width="300" height="300"/>

***Rails: 90 req / s***

***Sails : 433 req / s***

## Querying The Database

This test queries the DB gets 10 results from it and renders the data as JSON response.

Rails

<img src="/images/rails_json_db.png" width="300" height="300"/>

Sails

<img src="/images/sails_json_db.png" width="300" height="300"/>

***Rails: 124 req / s***

***Sails: 432 req / s***

## The Result

Even though i don’t think that these micro benchmarks applies to real world situations it’s also a little informative about the whole thing. As you can see from the results Sails is pretty fast sometimes 3-4x faster than Rails. Though as a framework Sails is the new kid on the block and has a lot to mature up and become a true Rails alternative. Currently it’s growing up in a good pace and the features are really good. I really like the Rails style command line ( model, controller scaffolding e.g) also the convention is the same.

I think in one or two years it’s gonna be not the defacto but a really big player in Node ecosystem.

What do you guys think?




