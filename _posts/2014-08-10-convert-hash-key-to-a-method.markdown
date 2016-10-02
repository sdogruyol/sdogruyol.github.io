---
title: Convert Hash Key to a Method
date: '2014-08-10 15:54:32 +0300'
date_gmt: '2014-08-10 15:54:32 +0300'
categories:
- Ruby
tags:
- hash
- key
- object
- rails
- ruby

---
Consider you have a Hash like this or you got a method which returns as a result.

    hash = { name: 'Serdar', last_name: 'Doğruyol'}

    hash.name # undefined method `name' for {:name;"Serdar", :last_name:"Doğruyol"}:Hash


Unfortunately when you try to access the hash key as you would in a normal object you get an 'undefined method' error.
Luckily we can solve this easily with some Ruby magic :)

    require 'ostruct'
    hash = { ad: 'Serdar', soyad: 'Doğruyol'}
    hash = OpenStruct.new hash
    hash.ad # Serdar

Now you can use your hash as an object and access the keys with method calls.

Happy hacking &lt;3
