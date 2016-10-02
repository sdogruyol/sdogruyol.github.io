---
status: publish
published: true
title: Using Shoulda With Rails 4 And Test Unit
categories:
- Rails
tags:
- rails
- rails-4
- ruby
- shoulda
- testunit
comments: []
---
If you are upgrading from a previous Rails version or want to use <a title="Shoulda" href="https://github.com/thoughtbot/shoulda" target="_blank">Shoulda</a> with Rails 4 and Test Unit the possibility that your Shoulda matchers won't work with your test is pretty high.
In this case when you trying to run your tests it throws an error like below.

    undefined method `validate_presence_of' for UserTest:Class

It's because actually Shoulda is not getting loaded properly due to some namespace changes and thus leading to this error.To overcome this situation.We need to modify our test_helper.rb file adding the proper namespaces.


    require 'shoulda'

    class ActiveSupport::TestCase
      ActiveRecord::Migration.check_pending!

      include Shoulda::Matchers::ActiveRecord
      extend Shoulda::Matchers::ActiveRecord
      include Shoulda::Matchers::ActiveModel
      extend Shoulda::Matchers::ActiveModel

      ...
    end

Happy Hacking &lt;3
