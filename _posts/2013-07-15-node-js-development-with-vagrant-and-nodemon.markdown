---
layout: post
status: publish
published: true
title: Node.js Development With Vagrant And Nodemon
author:
  display_name: sdogruyol
  login: sdogruyol
  email: dogruyolserdar@gmail.com
  url: ''
author_login: sdogruyol
author_email: dogruyolserdar@gmail.com
wordpress_id: 75
wordpress_url: http://serdardogruyol.com/?p=75
date: '2013-07-15 08:09:14 +0300'
date_gmt: '2013-07-15 08:09:14 +0300'
categories:
- Software
tags:
- node.js
- nodemon
- supervisor
- vagrant
comments:
- id: 6
  author: Pieter
  author_email: pietboth@gmail.com
  author_url: ''
  date: '2014-04-13 02:56:02 +0300'
  date_gmt: '2014-04-13 02:56:02 +0300'
  content: Thanks so much for this. Tried both nodemon and node-dev and neither worked.
    nodemon -L worked, but it was ridiculously slow (even with same timezone as host).
    supervisor is much faster! Thanks!
- id: 7
  author: WordPress PL
  author_email: wordpresscodexpolska@gmail.com
  author_url: http://wordpresscodexpolska.wordpress.com
  date: '2014-05-17 22:11:08 +0300'
  date_gmt: '2014-05-17 22:11:08 +0300'
  content: |-
    Superb Blog!|
    These are really impressive ideas in about blogging. You have touched some nice points here.
    <a href="http://wordpresscodexpolska.wordpress.com" rel="nofollow">WordPress</a>
- id: 8
  author: Johm
  author_email: j.harold88454@gmail.com
  author_url: ''
  date: '2014-06-21 10:45:57 +0300'
  date_gmt: '2014-06-21 10:45:57 +0300'
  content: You are a god. Thank you. So much time wasted trying different shared folder
    methods, sshfs etc. Supervisor works perfectly and no noticeable delay on mine.
- id: 9
  author: Phillipp
  author_email: susannaabercrombie@yahoo.de
  author_url: http://Tesha.blogspot.com
  date: '2014-07-14 00:30:12 +0300'
  date_gmt: '2014-07-14 00:30:12 +0300'
  content: "It's hard to find your page in google. I found it on 11 spot, you should
    build quality backlinks , it will \nhelp you to increase traffic. I know how to
    help you, just type in google - \nk2 seo tricks"
- id: 1311573
  author: Paulo H. Alves
  author_email: paulohalves0@gmail.com
  author_url: ''
  date: '2014-12-28 21:40:25 +0200'
  date_gmt: '2014-12-28 21:40:25 +0200'
  content: Thank you. In operation!
---
<p>Today i've encountered a strange behaviour which makes Node development on a Vagrant box a bit struggling. The reason is that if you are using Nodemon on your Vagrant box to restart your process ( e.g your Express app ) on file changes sometimes it's not even detecting the changes correctly or taking too long.</p>
<pre>nodemon -L app.js</pre>
<p>After some digging i've found that you can use Nodemon with -L option to detect the changes on the Vagrant box. But as others reported it didn't work for me and cause the process to restart continuously.</p>
<p>As an alternative to Nodemon you can use Supervisor which basically does the same stuff. And works correctly on file changes.</p>
<p>To get Supervisor from npm</p>
<pre>sudo npm install -g supervisor</pre>
<p>To start your process with Supervisor</p>
<pre>supervisor app.js</pre>
<p>P.s : Supervisor can also take up to 3-4 seconds to restart and sync file changes.</p>
<p>&nbsp;</p>
