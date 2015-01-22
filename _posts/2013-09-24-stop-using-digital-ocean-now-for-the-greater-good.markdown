---
layout: post
status: publish
published: true
title: Stop using Digital Ocean Now! For the greater good !
date: '2013-09-24 15:47:41 +0300'
date_gmt: '2013-09-24 15:47:41 +0300'
categories:
- General
tags:
- digitalocean
- stop
comments: []
---
As you all know [DigitalOcean](https://www.digitalocean.com/) is the new rage for cloud hosting. I've also been a DO customer for like 7-8 months. I told everybody about DO that they're awesome, they've ssds, they're ultra cheap and etc. Thanks to my advises a lot of people started using and trusting their business to DO like i do.
However the truth about cheap servers all the one click distro bla bla is not so good. Starting this September things started to get ugly. First of all, the support which was really helpful and kind at the beginning has became much worse and somewhat hard to understand.
At the beginning of this month I've made a simple Ruby command line tool to dump my database to my existing Dropbox account to save the hassle [dump-to-cloud](https://github.com/Sdogruyol/dump-to-cloud).

I used my tool to dump my db on a daily basis. After 1 day or so i tried to reach my server but couldn't even ping or ssh to my server. I thought that DO was down but the truth was that they've locked my account without any notification. Can you imagine this? Those droplets in my account were serving 2 mobile apps with more than 25k users.  And you know every second is a lost customer. I've logged into my DO account from the web site and answered the ticket that i'm using a simple tool to dump the db. And after hours of dealing with support i've reopened my account.
But today without any notification or so i've received a email from DO.

    Oh no! We've found an issue with your account and issued you a new ticket that needs to be addressed as soon as possible.

    Please login to view the ticket:
    https://www.digitalocean.com/support
    Thanks so much,
    DigitalOcean

I've logged into my account and checked the ticket. It asked me to verify my account i was shocked because i've been a paying customer for more than 7  months.

![DigitalOcean Verifiy 1](/images/do_verify.png)

I have answered all the questions and the proof that i'm a real living person.
And here's the response that i got.

![DigitalOcean Verifiy 2](/images/do_verify_2.png)

What kind of answer is that? No reason, no information and all the droplets all your services e.g running on DO is now closed. Can you imagine a situation like this ? Now that my droplets are gone i've 25k angry users who're waiting for the service to be up. And the funny thing is that i've never expected something like this from DO .

<strong>So for your own good and for the greater good of your users. Don't use DO if you are running big apps or production websites . One day without any notice your account can get locked and you'll get an answer with no information. </strong>

Now to migrate and deal with all the angry users.

***Update for HN Users*** : Thanks all for your comments and time. Here are the applications that i was hosting on the applications.
IOS : https://itunes.apple.com/tr/app/yazar-gundemi/id608022610?mt=8
Android : https://play.google.com/store/apps/details?id=com.codehardz.yazargundemi&amp;hl=tr

***Second Update*** : I've sent DO the document to disclosure the information. Waiting for their answer.
