---
layout: post
status: publish
published: true
title: 'Rust vs Ruby: Building an API'
date: '2015-04-09 22:49:13 +0300'
categories:
- API
tags:
- ruby
- rust
- api
- json
comments: []
custom_js:
- chartkick
---

I've been itching to write this blog post for a while. Now that Rust is 1.0.0-beta
the time has come.

Recently i'm trying to learn Rust and dealing with lots of compile-time errors :)
As a Rubyist i felt very overwhelmed when i first saw the Rust syntax. But as i started
to understand the concepts all the overwhelming feeling is gone and now i'm pretty in love
with Rust.

As i'm experimenting with Rust i'd like to try something useful. Building a simple API and
seeing how it performs against Ruby. Our simple API will return a JSON response like

	{"first_name": "Hello", "last_name": "World"}

Let's get dirty :)

## Details

For both examples i don't want too much framework overhead that's why i'm gonna use microframeworks which are pretty close to metal.

* Rust: ***[nickel.rs](https://github.com/nickel-org/nickel.rs/)***
* Ruby: ***[cuba](https://github.com/soveran/cuba)***
	* Ruby: Thin
	* JRuby: Puma

First we gonna start with Rust

### Rust Application

For Rust implementation we gonna use ***[nickel.rs](https://github.com/nickel-org/nickel.rs/)***

Let's start with creating our project. Since this is not a library we gonna use --bin option.

	cargo new rust-json-api --bin
	cd rust-json-api

To use nickel we need to modify our ***Cargo.toml*** file like below.

	[package]
	name = "rust-json"
	version = "0.0.1"
	authors = ["Sdogruyol <dogruyolserdar@gmail.com>"]

	[dependencies.nickel]
	git = "https://github.com/nickel-org/nickel.rs.git"

	[dependencies.nickel_macros]
	git = "https://github.com/nickel-org/nickel.rs.git"

	[dependencies]
	rustc-serialize = "*"

And to implement our api here's our ***src/main.rs*** file

```rust
#[macro_use] extern crate nickel_macros;
extern crate rustc_serialize;
extern crate nickel;

use std::collections::BTreeMap;
use nickel::{Nickel,HttpRouter};
use rustc_serialize::json::{Json, ToJson};

fn main() {
	let mut server = Nickel::new();

	server.utilize(router! {
			get "**" => |_req, _res| {
				let person = Person{
					first_name: "Hello".to_string(),
					last_name: "World".to_string()
				};
				person.to_json()
			}
	});
	server.listen("127.0.0.1:9292");
}

#[derive(RustcDecodable, RustcEncodable)]
	struct Person {
	first_name: String,
	last_name: String,
	}

impl ToJson for Person {
	fn to_json(&self) -> Json {
			let mut map = BTreeMap::new();
			map.insert("first_name".to_string(), self.first_name.to_json());
			map.insert("last_name".to_string(), self.last_name.to_json());
			Json::Object(map)
	}
}
```

I know that you're pretty much scared after seeing this code :) Well don't worry this
is a simple JSON API.

To run

	cargo run --release

Open your browser and go to [http://localhost:9292](http://localhost:9292) to see it yourself :)

That's it for our Rust implementation.

### Ruby Application

For our Ruby application we gonna use ***[cuba](https://github.com/soveran/cuba)*** because it's pretty close to Rack.

To create our Cuba application we gonna use ***[cuba-generator](https://github.com/Sdogruyol/cuba-generator)***.

	gem install cuba-generator

Now that we have installed cuba-generator. Let's create and run our api :)

```
cuba new ruby-json-api --type api
cd ruby-json-api && rackup
```

I'm not gonna go into the details of Ruby implementation since it's pretty easy. You can
find the sample application at the links below.

Like the Rust application open your browser and go to [http://localhost:9292](http://localhost:9292) to see it in action.

<script src="//www.google.com/jsapi" type="text/javascript"></script>
<script src="/js/chartkick.js" type="text/javascript"></script>

## Benchmarks

I've really wondered how Ruby is gonna perform against Rust and that's why benchmarked the
apps using ***wrk***.

#### 100 connections

	wrk -c 100 http://localhost:9292

<div id="chart-100" style="height: 300px;"></div>
<script>
  new Chartkick.BarChart("chart-100", [{name: "Request Per Second", data: [["Rust", 69486],["JRuby",13498],["MRI",1549]]}, {name: "Timeout", data: [["MRI", 0],["JRuby", 252],["Rust",271]]}], {max: 100000});
</script>

#### 1000 connections

	wrk -c 1000 http://localhost:9292

<div id="chart-1000" style="height: 300px;"></div>
<script>
  new Chartkick.BarChart("chart-1000", [{name: "Request Per Second", data: [["Rust", 63979],["JRuby",12195],["MRI",1523]]}, {name: "Timeout", data: [["MRI", 2013],["JRuby", 2410],["Rust",2982]]}], {max: 100000});
</script>

## Conclusion

First of all Rust is really really fast serving nearly ***70k*** RPS which is expected given that it's a compiled language with great performance.

The most surprising thing is that JRuby is really fast when paired with Rack / Cuba serving nearly ***15k*** RPS which is a great accomplishment.

Lastly MRI serves around ***1.5k*** RPS on both 100 and 1000 connections. I think the GIL is at fault here.

So Rust or Ruby? Which one do you prefer?

Happy hacking <3

***P.S:*** You can find the sample apps on [Github](https://github.com/Sdogruyol/rust-vs-ruby)
