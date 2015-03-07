---
layout: post
status: publish
published: true
title: Customize Rails Generators to Avoid Generating Unnecessary Files
date: '2014-09-13 12:03:13 +0300'
date_gmt: '2014-09-13 12:03:13 +0300'
categories:
- Rails
tags:
- rails
- rails-4
- generator
comments: []
---
As you may already know when you a generate a new resource, model or controller there are bunch of other files that are also being generated for you.
Rails has a great way to customize the default generators as you wish. For example let's assume that we are creating an API for a mobile app. Let call this app <strong>sample_api</strong>
Let's create a controller for our <strong>sample_api</strong>

    rails g controller posts

![Rails Generator 1](/images/generator_1.png)

And you can see the files that are generated for you.
Well since this is an API, We don't need any assets (Javascript and CSS). Normally we delete that folders but when we generate another resource we need to do the same again. Instead let's customize our generator to disable those for us.
In our ***config/application.rb***

    config.generators do |g|
      g.stylesheets     false
      g.javascripts     false
    end


Now let's see the generator in action again.

![Rails Generator 2](/images/generator_2.png)

Voila :) As you can see assets are no longer being generated for us.
Customizing the generators can really improve our workflow. You can disable unwanted test or spec generation, disable assets or whatever you want. Hell, you can even create your own generators.
Happy Hacking &lt;3
