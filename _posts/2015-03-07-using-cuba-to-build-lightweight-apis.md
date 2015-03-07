---
layout: post
status: publish
published: true
title: Using Cuba to Build Lightweight APIs
date: '2015-03-07 12:03:13 +0300'
categories:
- Ruby
tags:
- ruby
- cuba
- api
comments: []
---

Nowadays building an API is a really common task. If you're a Ruby developer chances are really high that you're already using Rails for an API.

Well using Rails is OK but it's not your only choice. Also as you may already know Rails is not a perfect solution for a lightweight API. It has lots of dependencies, requires lots of resources e.g.

Luckily there are great alternatives for building lightweight and scalable APIs with Ruby. I'm pretty sure that you've already heard about [Sinatra](http://http://www.sinatrarb.com) but today i'm gonna be talking about [Cuba](http://cuba.is).

## What is Cuba?

***Cuba is a Ruby microframework for web development.***

That's exactly what Cuba is. It's a really lightweight microframework built upon ***Rack***. The source is less than 500 LOC and pretty easy to grok.

Cuba is simple to use here's a 'Hello World' page 

	hello.ru

```ruby
 require 'cuba'
	
Cuba.define do	
  on get do
    on root do
      res.write 'Hello World!'
    end
  end
end
	
run Cuba
```			

To run
	
	rackup hello.ru
	
Go to [http://localhost:9292](http://localhost:9292/) and see it in action :)

So starting with Cuba is pretty easy no generator, no boilerplate just a simple ***.ru*** file and we're on.

Okay now let's get to real point.

## Building a JSON API

Actually serving JSON is also pretty easy with ***Cuba***

Let's take our first example and modify it a little bit to serve our users as JSON. First of all let's create a ***config.ru*** file for our application so that rackup automatically boots up.


	config.ru
```ruby
require './api'

run Cuba
```

	api.rb
```ruby

require 'cuba'
require 'json'

Cuba.define do
  on.get do
    on root do
      res.write 'Hello World'
    end
    
    on 'users.json' do
      users = { first_name: 'Serdar', last_name: 'Dogruyol'}
      users << { first_name: 'Hakan', last_name: 'Dogruyol'}
      res.headers["Content-Type"] = "application/json; charset=utf-8"
      res.write users.to_json
    end
  end    
end    
```

Now we dont need to specify a file for rackup to boot our application since we created a ***config.ru*** file.

	rackup

Go to [http://localhost:9292/users.json](http://localhost:9292/users.json)

That's it now we're serving our beloved 'users' as a JSON API :) Isn't it awesome ? 

You can check the source code of the full sample on [Github](https://github.com/Sdogruyol/cuba-datamapper-sample).

***P.S***: To learn more about ***Cuba*** read the [Cuba Book](http://soveran.github.io/cuba/) and also be sure to check out [rack-protection](https://github.com/rkh/rack-protection) to secure your API.

Happy Hacking &lt;3
