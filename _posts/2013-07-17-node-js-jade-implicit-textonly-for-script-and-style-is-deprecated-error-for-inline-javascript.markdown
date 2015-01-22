---
layout: post
status: publish
published: true
title: Node.js / Jade 'Implicit textOnly for script and style is deprecated.' error
  for inline Javascript
date: '2013-07-17 13:50:23 +0300'
date_gmt: '2013-07-17 13:50:23 +0300'
categories:
- Software
tags:
- express
- jade
- node.js
comments: []
---
Yesterday while i was playing with a quick Node app built using Express (3.x). I used the inline Javascript tag to do some Socket.io stuff.
I encountered a strange error that i've never seen before. After checking my previous package.json files and the current one. I noticed the source of the error.

    dependencies": {
        "express": "3.0.6",
        "jade": "*",
        ........
    }

Seemed like the error was related to newer versions of Jade ( &gt; 0.32.x). As you can see from this gist (<a title="Deprecate implicit text-only for script and style" href="https://github.com/visionmedia/jade/pull/1036" target="_blank">https://github.com/visionmedia/jade/pull/1036</a>)  they deprecated implicit text-only for script and style.<span style="font-size: 1em;"> </span>
So we have to explicitly declare that the script is text only. By adding an . after script tag.
Instead of

        script
     // Your JS code

Use this

        script. // See the '.'
     // Your JS code

***P.S***  : If you dont want to break your current working apps which use inline JS dont use the ' * ' directive in your ' package.json ' for your Jade version.

Happy Hacking &lt;3