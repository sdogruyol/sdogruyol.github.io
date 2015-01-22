---
layout: post
status: publish
published: true
title: Using Stylus Mixins To Stay DRY!
date: '2014-02-04 10:53:27 +0200'
date_gmt: '2014-02-04 10:53:27 +0200'
categories:
- General
tags: []
comments: []
---
I've been a long time Stylus user since i've started playing with Node.js . Until recently i started to understand the real power of Stylus with Mixins.
Basically Mixins in Stylus are reusable code pieces and rulesets that can be applied to avoid code duplication. This leads to a cleaner and easier codebase to maintain.
Let's take a look at a simple but powerful Mixin.

    border-radius(n)
      -webkit-border-radius: n
      -moz-border-radius: n
       border-radius: n

As you can easily understand from the example these Mixin is a shortcut for handling border-radius with vendor prefixes.<br />
To use this we can simply

    border-radius(5px)

Which translates to

    webkit-border-radius: 5px
    -moz-border-radius: 5px
    border-radius: 5px

So simple yet so powerful, isn't it ?
Okay let's move onto a bit more advanced stuff. What if we want to pass multiple values or an expression as an argument to the Mixin ?<br />
Something like shorthand properties for box-shadow.

    box-shadow()
      -webkit-box-shadow: arguments
      -moz-box-shadow: arguments
      box-shadow: arguments

Now this looks interesting. We have a local variable called arguments. In Stylus this is a shortcut for accessing all the parameters that has been passed to the function. Also be aware that we are not using any argument placeholders when defining our Mixin.
For example we can call this Mixin with something like this

    box-shadow(0 0 2px rgba(51, 51, 51, 0.51))

Which translates to

    -webkit-box-shadow: 0 0 2px rgba(51, 51, 51, 0.51)
    -moz-box-shadow: 0 0 2px rgba(51, 51, 51, 0.51)
    box-shadow: 0 0 2px rgba(51, 51, 51, 0.51)

Voila ! We now have two useful Mixins. Pretty enlightening isn't it? To learn more about Mixins in Stylus. Take a look at the official documentation below.<br />
[Stylus Mixin Documentation](http://learnboost.github.io/stylus/docs/mixins.html)

Happy hacking &lt;3
