---
layout: post
status: publish
published: true
title: Using ActiveSupport StringInquirer for pretty String equality
date: '2014-08-28 09:59:58 +0300'
date_gmt: '2014-08-28 09:59:58 +0300'
categories:
- Uncategorized
tags: []
comments: []
---
ActiveSupport is pretty awesome. It has really awesome stuff hidden in it.***StringInquirer*** is one of them.

It lets you test ***String equality*** in a much more pretty way. Rails also uses StringInquirer for it's environment check.

You may recognize this

    Rails.env.production?

Instead of doing

    Rails.env == 'production'

So how can we use StringInquirer for ourselves.

Let's pretend that we have a String called status.

    status = ActiveSupport::StringInquirer.new 'is_used'
    status.is_used? # true
    status.is_new? # false

Happy hacking &lt;3

