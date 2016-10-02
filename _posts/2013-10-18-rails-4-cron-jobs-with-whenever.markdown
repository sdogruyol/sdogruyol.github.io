---
status: publish
published: true
title: Rails 4 Cron Jobs With Whenever

date: '2013-10-18 20:23:28 +0300'
date_gmt: '2013-10-18 20:23:28 +0300'
categories:
- Rails
tags:
- cron
- rails
- rails-4
- ruby
- whenever
comments: []
---
Cron jobs are really useful for recurring background jobs ( mailing, indexing etc.) .
In Ruby / Rails world there are plenty of options for background proccessing but most of them are not that simple and easy to being with. However Whenever from Javan Makhmali is pretty simple and easy to use. But most of the tutorials and usage guidelines are outdate and for Rails 2.x - 3.x.
To begin with include Whenever in your Gemfile

    gem 'whenever', require: false

And then

    bundle install

Make sure that your Whenever gem version <strong>is at least &gt;= 0.8.4</strong>. You can check it by doing.

    whenever -v

To initiate Whenever change to your root directory and

    wheneverize .

By doing so we gonna get a schedule.rb file in our config folder. You can use this file to create recurring background jobs and the DSL really useful. For example let's run a background job every hour.

    every 1.hour do
      runner "ClassName.hourly_job"
    end

To create the actual cron job.

    whenever --update-cron theNameOfCronJob

This will create an output like this.

    write crontab file updated

To confirm we can check the crontab by

    crontab -l

And the actual cron

    # Begin Whenever generated tasks for: theNameOfCronJob
    * * * * * /bin/bash -l -c 'cd /your/project/path &amp;&amp; bin/rails runner ''ClassName.hourly_job''''

    # End Whenever generated tasks for: theNameOfCronJob

You can also try the Cron job by running it from the console.

Note:
I've had some troubles while trying to use Whenever with Rails 4. The problem was that my Whenever version was not supporting Rails 4 and generating a Cron like this

    /bin/bash -l -c 'cd /path/to/project script/runner -e development '''Job.new''''

The problem is that ***script/runner*** is no longer available in Rails 4. That's why the script wasn't executed. To avoid this be sure to update your Whenever gem to 0.8.4.

Happy hacking &lt;3
