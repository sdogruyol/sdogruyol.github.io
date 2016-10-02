---
status: publish
published: true
title: Node.js Development With Vagrant And Nodemon
date: '2013-07-15 08:09:14 +0300'
date_gmt: '2013-07-15 08:09:14 +0300'
categories:
- Node.js
tags:
- node.js
- nodemon
- supervisor
- vagrant
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
