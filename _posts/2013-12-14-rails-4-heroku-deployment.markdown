---
title: Rails 4 Heroku Deployment
date: '2013-12-14 17:49:04 +0200'
date_gmt: '2013-12-14 17:49:04 +0200'
categories:
- Rails
tags:
- heroku
- rails
- rails-4
- ruby

---
Deploying Rails 4 to Heroku can be tricky and have some nuances that you have to take care of. I've struggled around to find solutions to little stuff and lost hours to get my Rails 4 app working in Heroku.
This guide is for people who want to deploy their Rails 4 app to Heroku without any waste of time.
## Step 1
First of all i'm assuming that you have a working Rails 4 app and already have an account for Heroku.
Cd into your app folder

    cd app_folder

## Step 2 : Gemfile Configuration
Heroku uses Postgresql as the Database that's why you need to add 'pg' gem for production and either remove 'sqlite' or move it to development group in your gemfile.
You also need to add Heroku plugins to make it work with Rails 4 nicely.

    group :production, :staging do
      gem 'pg'
      gem 'rails_12factor'
    end

    group :development do
      gem 'sqlite3'
    end

Now that your bundle is complete

    bundle install

## Step 3: Database Configuration
Configure your database.yml and change your production credentials. You can get your database name, password and user from your Heroku account.

    production:
    adapter: postgresql
    database: your_db_name
    user: your_db_user_name
    password: your_db_user_password
    port: 5432
    pool: 5
    timeout: 5000

## Step 4: Production.rb Configuration
If you have JS, CSS files standing in your vender/assets folder add this to your production.rb

    config.assets.precompile += %w( *.css *.js )

Also make sure that rails is serving static assets

    config.serve_static_assets = true

## Step 5 : Git
Heroku uses Git for deployment so commit your files by doing.

    git add --all
    git commit -m "This is a commit"

Now that we have commited all our project files to Git let's get ready for Heroku.

## Step 6: Heroku
Firstly Login into your Heroku account with your credentials.

    heroku login

After login be sure to be in your project root folder and do

    heroku create

This will create a remote branch of your project for Heroku deployment.
And lastly deploy the application with.

    git push heroku master

Congrats you have pushed your application to Heroku. Now you can visit your application with

    heroku open

And if you have any pending migrations don't forget to migrate with

    heroku run db:migrate

Happy hacking &lt;3

